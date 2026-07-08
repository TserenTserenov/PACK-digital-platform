---
id: DP.FM.206
title: "DDL в ensure_schema() = ACCESS EXCLUSIVE lock при каждом запуске воркера"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / database-migrations
epistemic_stage: confirmed
valid_from: 2026-07-06
source: "session-close 2026-07-03 (WP-454, git diff DS-my-strategy e21aa4206)"
related:
  see_also: [DP.FM.205]
---

# DP.FM.206 — DDL в ensure_schema() = ACCESS EXCLUSIVE lock при каждом запуске воркера

## Описание

Паттерн `ensure_schema()` (bootstrap функция, исполняющая SQL-файл при старте воркера) удобен для гарантии схемы при деплое. Ловушка: если в schema.sql добавить DDL (ALTER TABLE, DROP/ADD CONSTRAINT), эти операции выполняются при КАЖДОМ запуске воркера, включая cron-задания. `ALTER TABLE` берёт `ACCESS EXCLUSIVE` lock на всю таблицу — блокирует все read/write во время выполнения.

## Пример

WP-454, `panel_worker.py:137`: `ensure_schema()` читает `wp417-panel-schema.sql` целиком. Перенос `ALTER TABLE ... ADD CONSTRAINT` в тот же файл → lock при каждом ночном досчёте carry-over (несколько раз за ночь).

## Диагностика

Тест: «Этот DDL-оператор идемпотентен И безопасен при N выполнениях подряд?»
- `CREATE TABLE IF NOT EXISTS` → ОК (idempotent, нет lock-проблем)
- `ALTER TABLE ... ADD/DROP CONSTRAINT` → НЕ ОК → выносить из ensure_schema

## Исправление

Одноразовые миграции (изменение типа, добавление constraint) — обязательно в отдельный migration-файл: `scripts/sql/migrations/NNNN-description.sql`, применяемый **вручную один раз** и никогда не вызываемый автоматически.

`ensure_schema()` должен содержать только CREATE IF NOT EXISTS / INSERT INTO ... ON CONFLICT DO NOTHING операции.
