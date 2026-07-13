---
id: DP.FM.270
type: failure-mode
title: "Дрейф bootstrap-скрипта и миграций БД при множественных версиях сервиса"
trust: observed
epistemic_stage: confirmed
domains: [database, migrations, multi-version, schema-drift, bootstrap]
source_session: "2026-07-08 session-close (git diff DS-ecosystem-development, WP-117, commit 0474b3c)"
valid_from: 2026-07-12
schema_version: 1
related:
  see_also: [DP.FM.042]
---

# DP.FM.270 — Дрейф bootstrap-скрипта и миграций при множественных версиях сервиса

## Симптом

Bootstrap-скрипт одной версии сервиса (например, pilot-ветка) незаметно «уезжает» от состояния схемы БД, созданного миграциями другой версии (prod-ветка). Итог: Railway-схема pilot содержит таблицы/колонки не из той миграции, что в prod. Обнаруживается только при явном сравнении ожидаемого состояния миграции с фактическим состоянием таблиц.

## Корень

В архитектуре «несколько версий сервиса, одна БД» у каждой версии может быть собственный bootstrap-скрипт, инициализирующий схему «снуля». Этот скрипт — самостоятельный источник дрейфа, независимый от migration-инструмента. Если версии развиваются параллельно, bootstrap pilot-версии не синхронизируется автоматически с миграциями prod-ветки.

## Профилактика

**Правило:** bootstrap-скрипт проверяет, что фактическое состояние схемы соответствует ожидаемому по последней migration, ИЛИ делегирует инициализацию тем же migration-инструментам (Alembic, Flyway, raw SQL migrations).

**Тест:** «bootstrap-скрипт читает migration-state и сравнивает с фактической схемой перед запуском?»
- Нет → дрейф возможен при наличии нескольких версий.

**Обнаружение:** явный diff `expected_schema(migration_state)` vs `actual_schema(DB)` при каждом деплое. Сигнал: таблицы/колонки из bootstrap, отсутствующие в migrations, или vice versa.

## Применимо к

- Сервисы с pilot/prod ветками, делящими схему БД (Railway, Neon multi-branch)
- Любые инсталляции с bootstrap-скриптом + отдельной migration-системой

## Связано

- DP.FM.042 — same-schema-neon-dbs (смежный: naming collision между разными БД; этот FM — drift внутри одного сервиса при множественных версиях)
