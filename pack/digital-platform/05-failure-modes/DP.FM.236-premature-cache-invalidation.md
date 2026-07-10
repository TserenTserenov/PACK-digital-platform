---
id: DP.FM.236
name: "Преждевременная инвалидация кеша: сброс ДО успешного promote"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-27
source: "session-transcript 2026-06-26-11; WP-446 code review iteration"
related:
  references: [DP.FM.124]
  see_also: []
tags: [cache, invalidation, race-condition, ordering, payment, state]
---

# DP.FM.236 — Преждевременная инвалидация кеша: сброс ДО успешного promote

## Паттерн

Cache invalidate вызывается ДО promote-операции. При неудаче promote кеш уже сброшен → следующий запрос пользователя получает stale-данные, будучи уверен в свежем состоянии.

## Пример

```python
# Неверный порядок (FM.236):
cache.invalidate(user_id)               # кеш сброшен
result = promote_subscription(user_id)  # неудача → кеш уже пуст
# Следующий запрос: cache miss → читает несогласованные данные

# Верный порядок:
result = promote_subscription(user_id)
if result.success:
    cache.invalidate(user_id)           # только после успеха
```

## Механизм

1. Cache invalidate вызывается до promote
2. Promote завершается ошибкой (network, DB, provider 4xx)
3. Кеш пуст, состояние системы НЕ изменилось
4. Следующий запрос: cache miss → читает из источника → видит старое состояние, но с потерей cache hit

## Почему опасен

- Несогласованное состояние: кеш инвалидирован, данные не обновлены
- Трудно воспроизвести: проявляется только при failure promote (редкое событие)
- Первопричина скрыта: пользователь видит «не применилось», а не «ошибка»

## Тест

«Если promote завершился ошибкой — кеш уже сброшен?» → Да → FM.236.

## Лечение

Инвариант: **promote → на успех → invalidate**.

## Связи

- Смежный FM: DP.FM.124 (LRU cache для async-ресурса с lifecycle)
- Общий принцип: побочный эффект (invalidate) следует ПОСЛЕ изменения состояния (promote)
