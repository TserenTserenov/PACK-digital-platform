---
id: DP.FM.144
type: failure-mode
title: "Side-effect check блокирует primary user flow"
description: "Проверка побочного эффекта (catch-up, уведомление, напоминание) внутри основного flow блокирует пользователя до завершения уведомления."
pack: PACK-digital-platform
domain: digital-platform
valid_from: 2026-06-06
schema_version: 1
---

# DP.FM.144 — Side-effect check блокирует primary user flow

## Описание

Проверка side-effect (catch-up уведомление, reminder, notification) выполняется синхронно внутри основного пользовательского flow (запуск урока, оформление заказа, вход). Если side-effect pending → flow прерывается: пользователь застревает на уведомлении, не добирается до целевого действия.

## Симптом

Пользователь начинает урок — бот отправляет catch-up уведомление о пропущенном дне ПЕРЕД стартом урока. Урок не начинается. Пользователь видит уведомление вместо занятия.

## Корень

```python
# Anti-pattern: side-effect check BEFORE primary action
if pending_catchup(user_id):
    send_catchup_notification(user_id)
    return  # или await без перехода к уроку
start_lesson(user_id)  # никогда не достигается
```

## Правило

Side-effect уведомления (catch-up, reminder, promotional) должны:
1. Отправляться **после** основного flow (fire-and-forget), или
2. Выполняться **асинхронно** (background task, не blocking await).

```python
# Correct pattern
await start_lesson(user_id)
asyncio.create_task(send_catchup_if_needed(user_id))  # после урока
```

## Тест

«Если catch-up check вернёт True — пользователь всё ещё доберётся до целевого действия в этом же запросе?» Нет → side-effect на wrong path.

## Применимость

Бот-интерфейсы, wizard-flow с notification triggers, любой UI с inline side-effect checks.

## Связи

- WP-330 (marathon bot, catch-up bug fix, commit `95347138c`)
- See: DP.FM.034 (async without await), DP.D.084 (read-path side effects)
