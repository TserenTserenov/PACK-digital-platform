---
id: DP.M.230
name: "Двухуровневая защита async replay-loop от infinite retry (outer + per-event wait_for)"
type: method
domain: event-driven-worker / asyncio resilience
trust: experiential
epistemic_stage: confirmed
status: active
valid_from: 2026-05-29
schema_version: 1
source: "session-close 2026-05-29 (peer-session 2026-05-29-10-event-loop-stall-replay)"
last_updated: 2026-08-01
---

# DP.M.230 Двухуровневая защита async replay-loop от infinite retry

## Решение

Комбинировать два уровня `asyncio.wait_for` в async replay-цикле: outer cap на весь цикл + per-event timeout с `continue`.

## Проблема

Поштучный `wait_for(process_event, 60s)` без внешнего cap = infinite retry: при periodic replay через 5 мин воркер снова упирается в тот же зависший event. Только outer без per-event — скрывает диагностику.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Отказоустойчивость ↔ диагностика | Per-event timeout позволяет продолжать обработку, но без outer cap зависший event будет повторяться бесконечно; outer cap останавливает цикл, но скрывает, какой именно event завис |
| Outer cap ↔ per-event granularity | Жёсткий потолок на весь цикл защищает от infinite retry, но если он слишком мал — нормальная пачка events не успевает обработаться |
| Cursor advance ↔ непрерывность обработки | `continue` позволяет advance'нуть cursor, но event теряется; `raise` сохраняет event для повторной попытки, но блокирует всё остальное |

## Паттерн

```python
# Outer: жёсткий потолок всего replay-цикла
await asyncio.wait_for(replay_from_cursor(conn, domain), timeout=240)

# Inner (внутри replay_from_cursor): per-event timeout с continue
try:
    await asyncio.wait_for(process_event(event), timeout=60)
except asyncio.TimeoutError:
    logger.warning(f"event {event_id} timeout, skipping")
    continue  # cursor advance, не raise
```

## Ключевые инварианты

1. **`continue` вместо `raise`** при per-event timeout: cursor advance, event не блокирует цикл.
2. **`asyncio.TimeoutError` из `pool.acquire(timeout=X)` в asyncpg (Python <3.12)** — тот же тип, ловится одним `except asyncio.TimeoutError`.
3. **`raise` = cursor не advance** → следующий periodic replay повторяет тот же event.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Per-event `wait_for` кажется достаточным | Практикующий добавляет timeout на обработку одного event и считает проблему решённой — не замечая, что periodic replay снова и снова приводит к тому же зависшему event |
| `raise` вместо `continue` при timeout | Инстинктивно хочется «сохранить event для повторной попытки», но при periodic replay это означает, что cursor не advance'ится и весь цикл застревает на одном event |
| Бесконечный retry не замечается, пока не накопится | Проблема проявляется не сразу, а через часы или дни; практикующий не видит её в локальных тестах и не добавляет outer cap |

## Условия применимости

- asyncio worker с periodic replay-cursor
- Possible зависание отдельных events (half-open TCP, DB timeout)
- Periodic replay в loop (не только event-driven NOTIFY)
