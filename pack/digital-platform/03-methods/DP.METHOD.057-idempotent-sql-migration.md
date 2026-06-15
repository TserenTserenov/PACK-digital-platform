---
id: DP.METHOD.057
name: Идемпотентные SQL-миграции
type: method
status: active
valid_from: 2026-06-15
summary: "Миграция БД должна быть безопасна при повторном запуске: проверка «уже существует» перед созданием, проверка «ещё существует» перед удалением."
related:
  see_also:
    - DP.D.144   # Пустой output контрольной роли ≠ «нет находок»
created: 2026-06-15
updated: 2026-06-15
tags: [database, migration, idempotency, schema, sql]
source: "peer-session 2026-06-15-23-ke-candidates-review (MIM.D.036 + DP.D.144-146 review)"
schema_version: 1
---

# DP.METHOD.057 — Идемпотентные SQL-миграции

> **Применяется:** при написании и ревью любого скрипта изменения схемы БД (DDL-миграции, seed-скрипты, rollback-скрипты).

## §0 Назначение

Миграция, выполняемая повторно, должна не падать и не создавать дублей. Это важно при:
- CI/CD retry (деплой упал на полпути и перезапустился)
- Multi-instance deploy (несколько воркеров запустили миграцию одновременно)
- Ручном повторном запуске после сбоя

## §1 Принцип

**Идемпотентная миграция = «guard перед действием».**

| Операция | Guard-условие |
|----------|--------------|
| CREATE TABLE / CREATE INDEX / ADD COLUMN | `IF NOT EXISTS` или эквивалент |
| DROP TABLE / DROP COLUMN / DROP INDEX | `IF EXISTS` или эквивалент |
| INSERT | `ON CONFLICT DO NOTHING` / upsert вместо чистого INSERT |
| ALTER ... ADD CONSTRAINT | проверить наличие constraint перед добавлением |

**Псевдокод (language-agnostic):**

```
# Создание — safe
if not exists(target):
    create(target)

# Удаление — safe
if exists(target):
    drop(target)

# Вставка — safe upsert
insert(row) on_conflict(key) do_nothing | do_update
```

## §2 Инвариант

**«Миграция выполнена» ≠ «миграция идемпотентна».** Одноразовый успех не гарантирует безопасность повторного запуска.

Признак нарушения: миграция упала с «already exists» / «does not exist» — значит, guard отсутствует.

## §3 Ревью-чеклист

- [ ] Каждый `CREATE` — имеет guard «IF NOT EXISTS» (или аналог платформы)
- [ ] Каждый `DROP` — имеет guard «IF EXISTS»
- [ ] Каждый `INSERT` — является upsert или имеет `ON CONFLICT DO NOTHING`
- [ ] `ADD CONSTRAINT` — проверено, что constraint не существует заранее
- [ ] Повторный прогон на dev-схеме прошёл без ошибок

## §4 Примечание (платформо-специфичное)

Конкретные DDL-шаблоны для отдельных платформ (PostgreSQL `DO $$ ... EXCEPTION WHEN duplicate_object ...$$`, MySQL `CREATE TABLE IF NOT EXISTS`, SQLite и т.д.) размещать в DS-документации конкретного проекта, а не в этом Pack. Принцип (§1-§3) не зависит от СУБД.
