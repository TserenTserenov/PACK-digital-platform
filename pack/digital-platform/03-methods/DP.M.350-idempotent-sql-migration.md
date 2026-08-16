---
id: DP.M.350
name: "Idempotent SQL migration: DDL-guards для safe re-run"
name_ru: "Идемпотентная SQL-миграция: DDL-guards для безопасного повторного запуска"
name_en: "Idempotent SQL migration: DDL-guards for safe re-run"
summary: "Каждый DDL-оператор в миграции должен быть idempotent: CREATE TABLE IF NOT EXISTS, DROP IF EXISTS, DO $$ BEGIN ... EXCEPTION WHEN duplicate_object ... END $$ для CREATE ROLE / GRANT. Принцип: миграция безопасна для N-кратного выполнения с одинаковым конечным состоянием."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: database
valid_from: 2026-06-15
related:
  see_also: [DP.M.303]
tags: [database, migration, SQL, DDL, idempotent, postgresql, schema]
source: "WP-410 peer-session 2026-06-15-04 (a2-idempotent.sql, a2-migration-runbook.md), migration 230 fix"
schema_version: 1
---

# DP.M.350 — Idempotent SQL migration: DDL-guards для safe re-run

## Описание

Миграция без idempotency-guard ломает повторное применение: первое выполнение ставит схему, повторное падает на «relation already exists». Это блокирует CI re-run, частичные применения и восстановление после сбоя.

**Принцип:** миграция = идемпотентный SQL, который безопасно запустить N раз с одинаковым конечным состоянием.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Краткость DDL ↔ безопасность re-run | `CREATE TABLE t (...)` короче и привычнее, но `CREATE TABLE IF NOT EXISTS` требует дополнительных слов ради устойчивости к повторному запуску |
| Скорость первого прогона ↔ устойчивость к CI re-run | Миграция без guards применяется быстрее в первый раз, но падает при повторе (flaky test, retry) — guards стоят немного больше времени на написание, но убирают целый класс инцидентов |
| Простой синтаксис ↔ DO $$ EXCEPTION-обёртка | `CREATE ROLE`/`GRANT` не имеют нативного `IF NOT EXISTS` в PostgreSQL — идемпотентность требует более многословной конструкции с перехватом исключения |
| Доверие к history-tracking (Alembic/Flyway) ↔ raw psql без state | Инструменты с историей применения избавляют от нужды в guards на уровне SQL, но `psql -f` без tracking полагается только на сам DDL как единственную защиту |

## Guards по типу DDL

| Оператор | Idempotent-вариант |
|----------|-------------------|
| `CREATE TABLE` | `CREATE TABLE IF NOT EXISTS` |
| `CREATE INDEX` | `CREATE INDEX IF NOT EXISTS` |
| `DROP TABLE` | `DROP TABLE IF EXISTS` |
| `CREATE ROLE` | `DO $$ BEGIN CREATE ROLE r; EXCEPTION WHEN duplicate_object THEN NULL; END $$` |
| `GRANT` | Проверить через `pg_has_role()` или обернуть в `DO $$` с `EXCEPTION` |
| `ALTER TABLE ADD COLUMN` | `DO $$ BEGIN ALTER TABLE t ADD COLUMN c type; EXCEPTION WHEN duplicate_column THEN NULL; END $$` |

## Тест корректности

> «Прогнал миграцию дважды подряд на чистую БД — упала ли на втором прогоне?»

- **Да** → DDL не idempotent → переписать с guards.
- **Нет** → паттерн соблюдён.

## Применимость

Универсально для любой schema-migration системы:
- Raw SQL (`psql -f migration.sql`)
- Alembic (PostgreSQL op с `if_not_exists=True`)
- Flyway (`CREATE TABLE IF NOT EXISTS` поддерживается нативно)
- Knex / Prisma (нативные флаги или `raw()` с guard)

## Когда особенно важно

- CI re-run при flaky test (миграция запускается повторно)
- Blue-green deployment (миграция применяется на двух инстансах)
- Disaster recovery (частично-инициализированная БД)
- Деплой без history-tracking (`psql -f` без flyway/alembic state)

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| «На локальной машине сработало один раз — миграция готова» | Тестирование идемпотентности пропускается, потому что первый прогон на чистой БД всегда проходит успешно независимо от наличия guards |
| «`CREATE TABLE` — простой случай, guards не нужны» | Внимание фокусируется на сложных операторах (`CREATE ROLE`, `GRANT`), а частые и «очевидные» `CREATE TABLE`/`CREATE INDEX` пропускаются без `IF NOT EXISTS` |
| «Flyway/Alembic сам отследит состояние — SQL можно не защищать» | Практикующий перекладывает ответственность на history-tracking инструмент, забывая, что частичный сбой применения оставляет БД в промежуточном состоянии, где guards всё равно нужны |
| «GRANT — это же просто права, не DDL в строгом смысле» | GRANT не воспринимается как объект idempotency-дисциплины наравне с CREATE/DROP, хотя повторный запуск может так же упасть или дать неожиданный побочный эффект |

## Связи

- [DP.M.303] — Production DDL через gated-шаг: complementary паттерн. DP.M.303: *как деплоить* DDL (отдельный gated-шаг, не из app startup). DP.M.350: *каким должен быть* DDL (idempotent). Вместе дают полный паттерн безопасной schema-evolution.
