---
id: DP.M.329
name: "Идемпотентность вебхука на уровне ограничения БД (ON CONFLICT DO NOTHING)"
type: method
domain: digital-platform
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-06-25
schema_version: 1
source: "peer-session 2026-06-25-02 (WP-427 Ф6.2), neon-migrations 30c22a7 — UNIQUE(ory_id, external_id)"
---

# DP.M.329 Идемпотентность вебхука на уровне ограничения БД (ON CONFLICT DO NOTHING)

## Описание

Webhook-приёмник с at-least-once доставкой (Discourse, платёжные провайдеры) может повторно доставить одно и то же событие. Дедуп кладётся на **ограничение схемы БД**, а не на app-side проверку «видел ли я это уже?». Идемпотентность приёмника = свойство схемы, не ветка кода.

## IPO

**Вход:** входящее событие с естественным уникальным ключом (например, `(ory_id, external_id)`)
**Процесс:** `UNIQUE(...)` constraint + `INSERT ... ON CONFLICT DO NOTHING`
**Выход:** строка вставлена при первой доставке; повтор молча проигнорирован (без возврата, без исключения)

## Почему не app-side проверка

`SELECT ... → if exists skip → else INSERT` несёт:
- **TOCTOU-гонку** при параллельных доставках одного события (оба SELECT возвращают «нет» → двойной INSERT);
- лишний round-trip на чтение перед записью.

`ON CONFLICT DO NOTHING` атомарен в одном запросе — гонка невозможна.

## Тест применимости

«При двойной доставке вебхука второй INSERT гасит база или код должен сам помнить?» Гасит база → применять DP.M.329.

## Отличие от DP.M.213 (UPSERT + xmax=0)

Сиблинг-идиома, другая цель:
- **DP.M.213** (`ON CONFLICT DO UPDATE ... RETURNING (xmax=0)`) — нужно **детектировать** первый-vs-повтор (вернуть `is_new` bool для бизнес-логики «первый раз?»).
- **DP.M.329** (`ON CONFLICT DO NOTHING`) — нужно **тихо проглотить** дубль redelivery, возврат не требуется.

Тест выбора: «нужно ли узнать, первый ли это раз?» Да → DP.M.213; нет (просто отбросить дубль) → DP.M.329.

## Применение

- Discourse-webhook (захват постов клуба, WP-427 Ф6.2)
- Любой event-ingest / webhook-приёмник с at-least-once доставкой, где дубль нужно отбросить без обработки

## Связи

- Контраст: DP.M.213 (UPSERT + xmax=0 — детекция INSERT vs UPDATE)
- Родственное: DP.METHOD.057 (идемпотентная SQL-миграция), DP.M.086 (notification-log cheap idempotency)
- Источник: peer-session 2026-06-25-02, neon-migrations 30c22a7
