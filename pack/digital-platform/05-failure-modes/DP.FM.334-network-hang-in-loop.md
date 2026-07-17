---
id: DP.FM.334
name: "Network hang in iterator loop"
type: failure-mode
status: active
valid_from: 2026-05-29
source: "WP-149 event-loop stall replay; DS-my-strategy/inbox/captures.md:740-746"
related:
  see_also: [DP.FM.300]
tags: [asyncpg, pool, acquire, timeout, iterator, network, half-open]
---

# DP.FM.334 — Network hang in iterator loop

## Симптом

Async-worker в цикле обработки событий внезапно замирает: нет ошибки, нет прогресса, CPU низкий. Процесс может висеть минутами до разрыва TCP ОС.

## Механизм

```python
# Антипаттерн: command_timeout защищает SQL, но не ожидание в очереди пула
async with pool.acquire():        # ← ждёт вечно по умолчанию
    await conn.fetch("SELECT ...", timeout=30)
```

`command_timeout` защищает только исполнение SQL. Очередь за соединением (`pool.acquire()`) не имеет таймаута. При half-open TCP worker зависает именно на `acquire()`, а не на SQL.

## Фикс

```python
async with pool.acquire(timeout=30.0):
    await conn.fetch("SELECT ...", timeout=30)
```

Явный таймаут на checkout соединения в каждом месте цикла.

## Тест

«Есть ли в цикле `pool.acquire()` без `timeout`?» Да → вероятно DP.FM.334.

## Источник

WP-149 event-loop stall replay fix; DS-my-strategy/inbox/captures.md:740-746.
