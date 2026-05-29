---
id: DP.FM.104
name: Отсутствие обратной функции identity-lookup
type: failure-mode
domain: digital-platform
pack_refs:
  - source: DP.ARCH.006
status: active
valid_from: 2026-05-28
schema_version: 1
---

# DP.FM.104 — Отсутствие обратной функции identity-lookup

## Описание

При построении новых интеграций прямой lookup (by primary key: chat_id → внешний ID) реализован, обратный (by external username: discourse_username → chat_id) — нет. Новые webhook-обработчики не могут замапить внешние идентификаторы на внутренние.

## Симптом

Webhook получает `discourse_username`, но функции `get_chat_id_by_discourse_username()` не существует в `db/queries/`. Результат: обработчик падает или делает лишний SELECT-join вручную.

## Паттерн

Прямой lookup: `chat_id → discourse_username` (реализован)
Обратный lookup: `discourse_username → chat_id → ory_id` (не реализован)

Цепочка: `external_username → internal_id → ory_id`

## Профилактика

Перед реализацией любого webhook-обработчика проверить наличие полной цепочки lookup в `db/queries/`:
- прямой: `get_<domain>_account(chat_id)`
- обратный: `get_chat_id_by_<domain>_username(username)`

## Применение

- Discord/Discourse/GitHub webhook-обработчики
- Любая интеграция, где событие несёт external username, а не internal ID
