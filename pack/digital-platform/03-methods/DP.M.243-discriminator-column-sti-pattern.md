---
id: DP.M.243
title: Discriminator Column (STI Pattern) — расширение таблицы вторым видом записей
type: method
domains: [database, schema-evolution, platform]
trust: confirmed
epistemic_stage: validated
valid_from: 2026-05-31
source: WP-373 (scope-control), session 2026-05-31
pack_refs:
  - DP.SC.165 (bridge-scope service clause)
  - DP.SC.163 (agent-scope service clause)
---

# DP.M.243 — Discriminator Column (STI) для расширения таблицы вторым видом записей

## Суть

При появлении второго «вида» записей в таблице (например, bridge-scope рядом с agent-scope) вместо создания отдельной таблицы — добавить discriminator-колонку `scope_kind TEXT NOT NULL DEFAULT 'existing_type'` и опциональные поля нового вида.

## Алгоритм применения

1. Оцени общность: ≥70% колонок совпадают у обоих видов — STI применим.
2. Добавь `scope_kind TEXT NOT NULL DEFAULT 'legacy_value'` через `ALTER TABLE ADD COLUMN ... DEFAULT`.
3. Добавь nullable-колонки для специфических полей нового вида.
4. Обнови query-path: `WHERE scope_kind = 'new'` / `WHERE scope_kind = 'legacy'`.
5. Boundary condition: при ≥4 видах с расходящимися полями — пересмотреть в сторону отдельных таблиц.

## Преимущества

- Единый query path — JOIN не добавляется.
- Переиспользование общих полей (`granted_by`, `reason`).
- Rollback = `ALTER TABLE DROP COLUMN`.

## Ограничения

- При N>3 видах или <70% общих колонок таблица ширится — рассмотреть polymorphic/joined table inheritance.

## Прецеденты

- WP-373: `agent_scopes_mvp` расширена для bridge-scopes через `scope_kind` + `allowed_operations TEXT[]`.

## Связи

- Аналог STI (Single Table Inheritance) в ActiveRecord.
- DP.SC.165 — первый потребитель паттерна в IWE.
