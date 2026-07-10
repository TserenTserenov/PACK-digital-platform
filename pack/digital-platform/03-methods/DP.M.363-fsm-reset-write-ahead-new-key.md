---
id: DP.M.363
name: "FSM reset via write-ahead new key"
name_ru: "Сброс сессии FSM через запись нового ключа, а не удаление"
name_en: "FSM session reset via write-ahead new key — race-condition-free alternative to delete-then-recreate"
summary: "Для сброса FSM-сессии записывать новый ключ с временным суффиксом (write-ahead), а не удалять существующий. Следующее сообщение получает новый session_id → FSM не находит записи → чистый контекст. Исключает race-condition при конкурентных сообщениях."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: agent-session-management
valid_from: 2026-06-22
related:
  see_also: [DP.M.028]
tags: [FSM, session-reset, write-ahead, race-condition, aiogram, PostgresStorage, agent-session]
source: "WP-428 Ф7, peer-сессия 2026-06-21-14, commit 7720858"
schema_version: 1
---

# DP.M.363 — Сброс сессии FSM через запись нового ключа (write-ahead reset)

## Описание

При необходимости сбросить FSM-сессию агента (например, по команде `/hermes_reset`) не удалять существующую запись, а записать новый ключ с временным суффиксом. Следующее обращение к агенту получит этот ключ как `session_id` → FSM не найдёт записи → агент начнёт чистый контекст.

**Ключевое отличие от delete-then-recreate:** при удалении + пересоздании возможен race condition: если пользователь успел отправить сообщение между delete и recreate — оно упадёт в старый или несуществующий контекст. Write-ahead избегает этого: запись атомарна, старый ключ остаётся нетронутым.

**Аналог:** immutable append (write-ahead log) вместо mutation.

## Algorithm

### Step 1: Генерируй новый session_id с временным суффиксом

```python
import time

new_session_id = f"tg-{user_id}-reset-{int(time.time())}"
```

### Step 2: Запиши новый ключ в FSM

```python
await state.storage.set_state(
    bot=bot,
    chat=chat_id,
    user=user_id,
    state=None,
    # используй новый session_id как ключ партиции
)
# или напрямую через storage key:
storage_key = StorageKey(bot_id=bot.id, chat_id=chat_id, user_id=user_id)
# aiogram: переключи ключ — следующее сообщение создаст новый контекст
```

### Step 3: Уведоми пользователя

```python
await message.answer("Контекст сброшен. Начинаем заново.")
```

Следующее входящее сообщение от пользователя получит новый `session_id` → FSM lookup вернёт пустой контекст → чистая сессия.

## When to use

- При реализации команды reset/restart для FSM-агента (aiogram, aiogram-contrib, любой FSM с persistence)
- При наличии риска конкурентных сообщений (пользователь нажал reset и сразу пишет)
- При PostgresStorage или любом внешнем FSM storage

## Тест применимости

«Может ли пользователь отправить сообщение между моментом сброса и созданием нового контекста?»
- Да → write-ahead new key (данный метод)
- Нет (синхронная, однопоточная обработка) → delete-then-recreate допустим

## Связи

- DP.M.028 (Stateless worker cursor pattern) — смежный принцип: состояние агента живёт во внешнем хранилище, не в памяти процесса
