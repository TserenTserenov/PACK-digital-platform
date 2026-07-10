---
id: DP.METHOD.153
type: method
domain: PACK-digital-platform
status: draft
summary: "При поэтапной миграции к новому consumer — вводить семантическую константу в общий контракт (Service Clause) вместо routing по строковым литералам. Константа объявляет намерение на стороне enqueue (legacy) и на стороне matcher (новый consumer). Тест: 'если поменяется строка — нужно менять в двух местах?' Да → нужна константа в контракте."
created: 2026-06-27
valid_from: 2026-06-26
version: v1.0
source: "session-transcript 2026-06-26-16, WP-418 Ф5 peer-сессия; PACK-digital-platform commit 21a905c (DP.SC.177); commit c6b9c4d1e"
related:
  see_also: [DP.M.025, DP.SC.177, DP.METHOD.151]
---

# DP.METHOD.153: Семантическая константа как handshake при staged migration

## Контекст

При поэтапной миграции proactive-сообщений от legacy producer к новому consumer требуется гарантия, что новый consumer получает только «свои» сообщения.

**Антипаттерн — routing по строковым литералам:**
```python
if message_type == "nudge":  # хардкоженная строка
    process_in_new_consumer()
```
Риск: при изменении строки "nudge" — ошибка в двух местах. Нет явного контракта.

## Метод

**Шаг 1. Определить класс в контракте (Service Clause)**

```python
CLASS_CAPPED = "capped"  # объявляется в shared contract, не в модуле
```
Зафиксировать в DP.SC.NNN: что consumer гарантированно получает только сообщения своего класса.

**Шаг 2. Enqueue через семантическую константу**
```python
# legacy producer — ссылается на контракт
enqueue(message_type=CLASS_CAPPED, payload=...)
```

**Шаг 3. Consumer matcher**
```python
# новый consumer — та же константа из контракта
if message.type == CLASS_CAPPED:
    process(message)
```

**Шаг 4. Canary + acceptance (обязательно)**
- 7-дневное canary-окно как формальный acceptance step, не просто observability
- По завершении: explicit accept/reject решение о расширении волны
- Pre-deploy Review-01: закрыть все Critical/High до deploy

## Тест надобности

«Если поменяется строка — нужно ли менять код в двух местах?»
- Да → нужна семантическая константа в общем контракте
- Нет → строковый литерал допустим

## Применимость

- Message queues (RabbitMQ, Redis Streams, Celery)
- Event-driven systems (event type routing)
- Feature flags с классами (class-based vs boolean)
- Тест: «есть ли coupling на строковый литерал между producer и consumer?» Да → извлечь константу
