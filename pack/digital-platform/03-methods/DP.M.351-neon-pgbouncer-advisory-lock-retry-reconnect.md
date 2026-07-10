---
id: DP.M.351
name: "Neon pgbouncer advisory lock: 20×30s retry + infinite reconnect outer loop"
domain: asyncpg-worker / neon-serverless / advisory-lock
trust: experiential
epistemic_stage: confirmed
status: active
valid_from: 2026-06-19
schema_version: 1
source: "session 2026-06-18, commit 9a64f5c (DS-IT-systems activity-hub)"
related:
  extends: [DP.M.206]
  see_also: [DP.FM.230]
tags: [asyncpg, neon, pgbouncer, advisory-lock, retry, reconnect, worker-resilience]
---

# DP.M.351 — Neon pgbouncer advisory lock: 20×30s retry + infinite reconnect outer loop

## Проблема

Neon pgbouncer держит Postgres-сессию живой ~5 минут после разрыва asyncpg-соединения. `pg_advisory_lock`, захваченный на этой сессии, остаётся удержанным — новый экземпляр воркера получает `pg_try_advisory_lock = false` в течение этих 5 минут.

Старый паттерн 9×10s (90 сек) недостаточен: при рестарте воркера за <5 мин новый экземпляр упирался в удержанный lock и тоже завершался с ошибкой.

## Решение

```python
_LOCK_RETRY_ATTEMPTS = 20   # 20 × 30s = 10 min — покрывает 5-мин hold Neon pgbouncer
_LOCK_RETRY_INTERVAL_SEC = 30

async def _connect_and_lock(dsn: str, lock_key: int) -> asyncpg.Connection:
    while True:  # infinite outer loop: reconnect при исчерпании попыток
        try:
            conn = await asyncpg.connect(dsn, statement_cache_size=0)
        except (asyncpg.InterfaceError, ConnectionDoesNotExistError, OSError) as exc:
            log.warning("connect failed: %s — retry in 30s", exc)
            await asyncio.sleep(30)
            continue

        for attempt in range(_LOCK_RETRY_ATTEMPTS):
            got = await conn.fetchval("SELECT pg_try_advisory_lock($1)", lock_key)
            if got:
                return conn  # успех
            log.debug("lock attempt %d/%d failed, sleeping %ds",
                      attempt + 1, _LOCK_RETRY_ATTEMPTS, _LOCK_RETRY_INTERVAL_SEC)
            await asyncio.sleep(_LOCK_RETRY_INTERVAL_SEC)

        log.warning("lock not acquired in %ds — reconnecting",
                    _LOCK_RETRY_ATTEMPTS * _LOCK_RETRY_INTERVAL_SEC)
        try:
            await conn.close()
        except Exception:
            pass
        # inner loop исчерпан → outer while продолжается (reconnect)
```

## Ключевые параметры

| Параметр | Значение | Обоснование |
|---------|---------|-------------|
| `LOCK_RETRY_ATTEMPTS` | 20 | 20 × 30s = 10 мин > 5-мин Neon pgbouncer hold window |
| `LOCK_RETRY_INTERVAL_SEC` | 30 | Баланс CPU idle / актуальность проверки |
| Outer loop | бесконечный | Reconnect при сетевом сбое без завершения процесса |

## Дополнительные ловушки

1. **`set_type_codec` пересоздаётся на каждом reconnect.** Кастомные type codecs asyncpg (jsonb, json) привязаны к соединению — при переподключении теряются. Пример:
   ```python
   # Вызывать после каждого connect, не только один раз при старте
   await conn.set_type_codec("jsonb", encoder=json.dumps, decoder=json.loads, schema="pg_catalog")
   ```

2. **`health.internal_metrics` должна существовать ДО деплоя воркеров.** Если таблица не создана (отсутствует migration), heartbeat-записи молча падают — алертер не видит воркера.

3. **`raise RuntimeError(...)` при исчерпании всех попыток** — обязателен для срабатывания `ON_FAILURE` политики Railway/systemd (см. DP.FM.230).

## Условия применимости

- asyncpg-воркер с `pg_advisory_lock` или `pg_try_advisory_lock` на Neon Serverless (pooler endpoint)
- Railway / systemd с политикой restart on non-zero exit
- Воркер статeless (advisory lock + bootstrap идемпотентны)

## Связи

- **DP.M.206** — общий метод fast-fail-and-restart: наш метод = конкретная реализация условия п.5 («Retry-with-backoff на старте»)
- **DP.FM.230** — FM return-вместо-raise: этот метод предполагает корректное завершение через raise при реальном исчерпании всех попыток

## Контекст

Три воркера (profiler_subscriber_neon, stage_transition_listener, anonymization_worker) замерзали из-за связки: (1) Neon pgbouncer 5-мин hold, (2) `return None` при неудаче (FM.230), (3) Railway не перезапускал. Фикс в commit 9a64f5c заменил 9×10s на 20×30s + infinite outer loop + raise при исчерпании. Верифицировано 2026-06-18: все три воркера стабильно работают.
