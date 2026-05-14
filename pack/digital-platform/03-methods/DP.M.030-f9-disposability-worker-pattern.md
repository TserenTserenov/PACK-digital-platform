---
id: DP.M.030
name: F9 Disposability — двухкомпонентный паттерн worker
type: method
status: active
valid_from: 2026-05-12
summary: "Для 12-factor F9 (Disposability) в event-driven workers нужны два независимых механизма: (1) SIGTERM handler для graceful shutdown, (2) cursor-based idempotency для crash safety. Только их комбинация даёт полный F9."
related:
  implements: [12factor-F9]
  see_also: [DP.M.028]
triggers:
  - "12-factor F9 audit"
  - "Проектирование нового event-driven worker"
  - "Crash/retry дубли в production"
created: 2026-05-12
source: "WP-307 Ф6+Ф9, 12 мая 2026 — PostgresStorage + CursorCache + batched-flush"
---

# DP.M.030 — F9 Disposability: двухкомпонентный паттерн worker

> **see 12-factor F9 (Disposability)**
> Применяется при: проектировании event-driven workers с внешним state (Neon, Redis, Kafka, PG).

## §1 Два независимых механизма

### Механизм 1: Graceful Shutdown (SIGTERM handler)

**Что делает:** завершает текущую задачу, освобождает ресурсы перед выходом.

```python
import signal, asyncio

async def shutdown(signal, loop):
    tasks = [t for t in asyncio.all_tasks() if t != asyncio.current_task()]
    for task in tasks:
        task.cancel()
    await asyncio.gather(*tasks, return_exceptions=True)
    loop.stop()

loop = asyncio.get_event_loop()
loop.add_signal_handler(signal.SIGTERM, lambda: asyncio.create_task(shutdown(signal.SIGTERM, loop)))
```

**Что гарантирует:** нет потери in-flight сообщений, нет zombie-коннекшенов к БД.
**Чего НЕ гарантирует:** безопасный restart после crash (только после SIGTERM).

### Механизм 2: Cursor-Based Idempotency

**Что делает:** сохраняет позицию в event stream в БД; restart читает с той же позиции.

```python
cursor = await db.fetchval("SELECT cursor_position FROM worker_state WHERE domain=$1", domain)
events = await db.fetch("SELECT * FROM events WHERE id > $1 LIMIT $2", cursor, batch_size)
await db.execute("UPDATE worker_state SET cursor_position=$1 WHERE domain=$2", last_id, domain)
```

**Что гарантирует:** безопасный restart после crash — дубли обрабатываются через UPSERT.
**Чего НЕ гарантирует:** освобождение ресурсов при останове (только при graceful shutdown).

## §2 Комбинация: полный F9

| Состояние | SIGTERM handler | Cursor idempotency | F9 |
|-----------|-----------------|---------------------|-----|
| Только handler | ✅ | ❌ | ⚠️ graceful без crash safety |
| Только cursor | ❌ | ✅ | ⚠️ crash safe без graceful |
| **Оба** | **✅** | **✅** | **✅ полный F9** |

**Тест F9:** «Убей процесс → подними → нет потери данных?» Да = F9 ✅.

## §3 CF Workers — встроенный F9

CF Workers имеют cold start <100ms по дизайну — F9 выполняется платформой. Механизмы §1-§2 не нужны (stateless by design).

## §4 Примеры в IWE

| Worker | Механизм 1 | Механизм 2 | Статус |
|--------|-----------|-----------|--------|
| Bot (aiogram) | asyncio SIGTERM | PostgresStorage (FSM) | ✅ F9 |
| W2/W3/W4 (projection) | SIGTERM handler | CursorCache + batched-flush | ✅ F9 |
| CF Workers (M1-M11) | — (платформа) | Stateless by design | ✅ F9 |
| A1-A6 (autonomous agents, launchd) | ❌ | ❌ cursor | ❌ F9 |

## Связи

- 12-factor F9: Disposability
- Смотри также: DP.M.028 (stateless-worker-cursor-pattern, F6)
