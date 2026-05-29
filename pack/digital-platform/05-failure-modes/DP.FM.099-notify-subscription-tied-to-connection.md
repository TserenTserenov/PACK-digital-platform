---
id: DP.FM.099
name: "NOTIFY-подписка живёт на коннекте — смерть conn = весь event-loop зомби"
type: failure-mode
domain: event-driven-worker / postgres-listen-notify
trust: experiential
epistemic_stage: confirmed
status: active
valid_from: 2026-05-28
schema_version: 1
source: "session-close 2026-05-28 (peer-session 13: projection-worker stall 222 мин)"
---

# DP.FM.099 NOTIFY-подписка живёт на коннекте — смерть conn = весь event-loop зомби

## Симптом

Event-driven воркер, использующий PG LISTEN/NOTIFY на single Connection: процесс жив, liveness/heartbeat-метрики зелёные, но cursor стоит, backlog растёт, `process_event` не вызывается. Внешний alerter на cursor lag ловит стагнацию через десятки или сотни минут.

## Механизм

При silent TCP drop коннект не закрывается явно: `is_closed()` возвращает `False`, потому что состояние «коннекта» в драйвере не синхронизировано с сетевым уровнем (half-open TCP). NOTIFY-callback, зарегистрированный на этом коннекте, перестаёт вызываться — драйвер не получает сетевые пакеты с уведомлениями. Очередь событий в процессе пуста, потому что её не наполняет колбек. `process_event` не вызывается → ни один counter событий не растёт. Liveness-метрика, опирающаяся на «процесс отвечает» или «heartbeat-таймер тикает», подтверждает живость, но фактически воркер не делает работу.

## Где встречается

Любой консьюмер PG LISTEN/NOTIFY на одиночном Connection (не pool):
- asyncpg `connection.add_listener()`
- psycopg `cursor.execute("LISTEN ...")` + polling
- libpq-wrappers с per-conn-subscription

## Признаки

- Cursor lag растёт линейно по времени с момента инцидента
- Counter обработанных событий = 0 за период
- `is_closed()` коннекта = False
- Процесс отвечает на SIGINFO / health-port
- Heartbeat в alerter тикает (если он опирается на таймер, не на counter событий)

## Митигация

1. **Time-based detection**: внешний alerter сравнивает cursor lag с порогом (например, >15 мин), а не counter событий.
2. **In-process keepalive ping**: периодический `SELECT 1` с `asyncio.wait_for` таймаутом → при timeout бросать исключение → host рестартует процесс (см. DP.M.206 fast-fail-and-restart).
3. **Pool вместо single conn**: подписку держать на отдельном dedicated Connection с явным keepalive, а основную работу — через pool.

## Анти-митигация

«`is_closed()` проверки достаточно» — нет, не достаточно. half-open TCP не детектируется без активного запроса.

## Связи

- Метод-митигация: DP.M.206 fast-fail-and-restart
- Источник: WP-200 (projection-worker stall 222 мин, 2026-05-28)
