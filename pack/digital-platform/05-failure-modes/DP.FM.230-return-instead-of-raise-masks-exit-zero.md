---
id: DP.FM.230
name: "return вместо raise в async-воркере маскирует сбой через exit(0)"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-19
source: "session 2026-06-18, commit 9a64f5c (DS-IT-systems activity-hub)"
related:
  references: [DP.M.206, DP.FM.027]
tags: [railway, worker, exit-code, advisory-lock, restart-policy, python, asyncpg]
---

# DP.FM.230 — return вместо raise в async-воркере маскирует сбой через exit(0)

## Паттерн

Python-функция инициализации воркера возвращает `return None` при неудаче (например, не смогла захватить advisory lock). Процесс завершается с exit-кодом 0. Платформа (Railway, systemd с `on-failure`, K8s с `restartPolicy=OnFailure`) интерпретирует exit(0) как штатное завершение и **не перезапускает** воркер.

```python
# Антипаттерн: воркер молча умирает
async def _connect_and_lock():
    for attempt in range(MAX_RETRIES):
        got = await conn.fetchval("SELECT pg_try_advisory_lock($1)", LOCK_KEY)
        if got:
            return conn
        await asyncio.sleep(30)
    return None  # exit(0) — Railway ON_FAILURE слеп к этому

# Правильно: явное исключение → exit(1) → рестарт
    raise RuntimeError("Could not acquire advisory lock after retries")
```

## Следствие

- Воркер не числится как CRASHED в Railway/systemd — выглядит как «штатно завершился»
- Heartbeat-записи прекращаются → алерт «нет heartbeat», но причина неочевидна
- Система работает без воркера до ручного перезапуска оператором

## Условия возникновения

1. Воркер использует `return None` или `return False` вместо `raise` при критической ошибке инициализации
2. Политика рестарта платформы срабатывает только на non-zero exit (`on-failure`, `ON_FAILURE: RESTART_ALWAYS`)
3. Heartbeat или мониторинг не покрывает случай «не запустился» (только «упал»)

## Тест обнаружения

«Воркер завершился — Railway/systemd показывает CRASHED?»
- Нет → exit(0) маскирует сбой. Проверить функцию инициализации на `return` вместо `raise`.

## Диагностика

```bash
# Проверить exit-код последнего завершения (Railway)
railway deployments --limit 5
# Если status=REMOVED (не CRASHED) → exit(0)

# В systemd:
journalctl -u worker.service --since "1 hour ago" | grep "Main process exited"
# code=exited, status=0/SUCCESS → exit(0) замаскировал сбой
```

## Профилактика

При любом критическом сбое инициализации воркера (не удалось подключиться, не удалось захватить lock, не найдена таблица): `raise RuntimeError(...)` вместо `return None`. Дополнить тестом: mock неудачного коннекта → assert `exit_code != 0`.

## Связи

- **DP.M.206** — метод fast-fail-and-restart: этот FM документирует антипаттерн, который DP.M.206 решает
- **DP.FM.027** — Railway missing autodeploy: схожий домен (Railway worker lifecycle), другой механизм

## Контекст

Три воркера (profiler_subscriber_neon, stage_transition_listener, anonymization_worker) периодически замерзали из-за Neon pgbouncer advisory lock. Первичный баг: функция `_connect_and_lock_*()` при неудаче lock делала `return None` → exit(0) → Railway `ON_FAILURE: RESTART_ALWAYS` не срабатывал. Фикс: `raise RuntimeError(...)` → exit(1) → Railway рестартует. Источник: session 2026-06-18, sessions/2026-06/2026-06-18-activity-hub-freeze-fix.md, commit 9a64f5c.
