---
id: DP.FM.166
title: "Schema-consumer contract breach: убрать required-поле без защиты KeyError в потребителе"
type: fm
pack: DP
tags: [schema, json-schema, consumer, contract, required-field, keyerror, schema-evolution]
status: draft
valid_from: 2026-06-16
schema_version: 1
---

# DP.FM.166 — Schema-consumer contract breach: required-поле убрано без защиты потребителя

## Описание

При переходе от строгой схемы (все поля в `required`) к более мягкой (часть полей — optional) разработчик убирает поля из `required` в JSON Schema, но не обновляет код потребителя. Потребитель читает поля через `dict["key"]` — при отсутствии поля в payload получает `KeyError`. Ошибка валидации на входе заменяется ошибкой внутри бизнес-логики.

## Условия возникновения

- JSON Schema с `required` array
- Потребитель (воркер, хендлер) читает поля как `payload["field"]` без `.get()`
- Required-set схемы уменьшается без одновременного обновления consumer code

## Симптом

`KeyError: 'field_name'` внутри воркера при обработке нового payload, который валиден по новой схеме.

## Механизм переноса ошибки

Старая схема: ошибка → `ValidationError` на входе (schema validation).
Новая схема (поле optional): схема принимает payload → воркер падает с `KeyError`.
Ошибка не устранена — перенесена из слоя валидации в слой бизнес-логики.

## Fix

При уменьшении `required`-set схемы — одновременно обновить все call-sites потребителя:
```python
# Было:
value = payload["field"]
# Стало:
value = payload.get("field", default_value)
```

## Тест обнаружения

«При изменении `required`-set схемы — все call-sites потребителя используют безопасный доступ (`.get()` / `try/except KeyError`) к новым optional-полям?» Нет → DP.FM.166.

## Грань

Это не backward-compatibility (возможность обработки старых payload). Это **одновременное изменение двух сторон парного контракта**: убрать поле из required = изменить контракт; все потребители контракта — участники этого изменения.

## Применимость

Любой event-driven pipeline с JSON Schema валидацией и несколькими воркерами-потребителями.

## Источник

session-transcript 2026-06-16; peer-session 2026-06-16-11-wp7-server-backlog-peer
