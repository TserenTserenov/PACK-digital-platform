---
id: DP.M.206
name: "Fast-fail-and-restart предпочтительнее in-process reconnect когда состояние коннекта = source-of-truth подписки"
type: method
domain: event-driven-worker / resilience
trust: experiential
epistemic_stage: confirmed
status: active
valid_from: 2026-05-28
schema_version: 1
source: "session-close 2026-05-28 (peer-session 13: stall fix)"
---

# DP.M.206 Fast-fail-and-restart предпочтительнее in-process reconnect когда состояние коннекта = source-of-truth подписки

## Решение

При детекции мёртвого коннекта (явный `is_closed()`, неудачный `SELECT 1` с таймаутом) бросать исключение и полагаться на host-уровневый рестарт (Railway, systemd, K8s, supervisord), вместо in-process reconnect с восстановлением подписок и состояния.

## Условия применимости

1. **Подписка несёт скрытое состояние:** NOTIFY-регистрация на коннекте, session-scoped advisory lock, in-memory replay-курсор, prepared statements, server-side cursors.
2. **Host даёт быстрый автоматический рестарт:** Railway, systemd с `Restart=on-failure`, K8s с restartPolicy=Always, supervisord — restart latency ≤5-10 сек.
3. **Bootstrap идемпотентен:** свежий процесс может пройти полный цикл инициализации без побочных эффектов на already-processed данные.
4. **Downstream обработка идемпотентна:** повторная обработка события не вызывает дублирующих side-effect (через idempotency-ключи, ON CONFLICT, exactly-once-через-курсор).
5. **Retry-with-backoff на старте:** для случая half-open TCP advisory lock — старый коннект ещё держит lock, новый процесс должен подождать и повторить acquire.

## Алгоритм

```python
async def health_loop(conn):
    while True:
        try:
            await asyncio.wait_for(conn.execute("SELECT 1"), timeout=5.0)
        except (asyncio.TimeoutError, ConnectionError):
            raise RuntimeError("Connection dead — restarting via host supervisor")
        await asyncio.sleep(30)
```

При исключении процесс завершается с non-zero exit code → host автоматически рестартует → свежий процесс выполняет полный bootstrap (открыть conn → re-subscribe NOTIFY → acquire advisory lock → replay cursor).

## Аргумент против in-process reconnect

Reconnect внутри процесса требует воспроизведения всего цикла bootstrap: re-subscribe NOTIFY (новый коннект → новый колбек), re-acquire advisory lock (контроль race condition с предыдущим owner), replay-курсор (откуда продолжить). Каждый из этих шагов — потенциальный источник багов, особенно edge cases (race conditions, частичное состояние). Стоимость поддержки in-process reconnect-логики > restart latency.

## Анти-применение

- Stateless воркер без подписок и locks → reconnect дешевле restart
- Host с медленным рестартом (cold-boot ВМ ≥30 сек) → пользовательский SLA пострадает
- Bootstrap не идемпотентен (например, миграции при старте) → restart создаст inconsistency
- Downstream не идемпотентен → повторная обработка вызовет дубликаты

## Связи

- Failure mode, который этот метод митигирует: DP.FM.099 NOTIFY-подписка живёт на коннекте
- Источник: WP-200 (projection-worker stall fix, 2026-05-28)
