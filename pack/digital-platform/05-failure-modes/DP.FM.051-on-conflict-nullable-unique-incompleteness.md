---
id: DP.FM.051
title: ON CONFLICT Incomplete Column List with Nullable UNIQUE
kind: FM
status: draft
trust: empirical
epistemic_stage: pattern
created: 2026-05-19
valid_from: 2026-05-19
sources:
  - commit b1f7b658 (WP-242 Ф9.3 tech-debt note — regression risk ON CONFLICT chunks)
related:
  distinguishes_from: [DP.FM.038, DP.FM.040, DP.FM.041]
  references: []
---

# DP.FM.051 — ON CONFLICT Incomplete Column List with Nullable UNIQUE

## Описание

Batch ETL/UPSERT использует `INSERT ... ON CONFLICT (col_a, col_b) DO UPDATE`, но целевая таблица имеет дополнительный constraint `UNIQUE (col_b, col_c)`, где `col_c` nullable. Postgres допускает множественные `NULL` в UNIQUE-индексах → дубликаты по `(col_b, NULL)` сегодня **не вызывают** UNIQUE violation, ETL проходит зелёным. Если позже схема меняется (col_c становится NOT NULL, или добавляется UNIQUE (col_b)), та же ETL начинает падать на повторной обработке.

«Зелёный smoke-test ≠ полная покрытость failure modes». Schema constraints с nullable columns создают режим «уверенно проходит сегодня, ломается на следующей миграции».

## Условие возникновения

- Batch ETL с UPSERT (`INSERT ... ON CONFLICT`)
- Целевая таблица имеет UNIQUE constraints не на тех же колонках, что ON CONFLICT clause
- Хотя бы одна из колонок UNIQUE constraint — nullable
- В рабочем datase часть строк имеет NULL в этой колонке

## Fix

При peer-review batch ETL / migration:

1. **Grep всех UNIQUE constraints** целевой таблицы (`\d <table>` или query к `pg_indexes`).
2. **Cross-check с ON CONFLICT column list** — все ли UNIQUE columns покрыты? (Логически: для каждого UNIQUE-набора либо все его колонки в ON CONFLICT, либо UNIQUE constraint избыточен.)
3. **Явная проверка nullable columns** в UNIQUE — если есть, зафиксировать риск schema-evolution в tech-debt-note и при следующей миграции либо расширить ON CONFLICT, либо сделать колонку NOT NULL с миграцией данных.

## Тест (триггер распознавания)

«Пишу UPSERT в таблицу с >1 UNIQUE constraint? Хотя бы одна колонка UNIQUE constraint nullable?» → Да → ON CONFLICT неполон, добавить tech-debt note или расширить clause.

## Применимость

- batch reindex (search index, knowledge graph chunks)
- ETL projection workers (event → derived state)
- любая UPSERT-семантика в Postgres с эволюционирующей схемой

## Отличие от смежного

- **DP.FM.038 (validator-silent-pass):** там validator пропускает баг; здесь баг скрыт NULL-семантикой UNIQUE.
- **DP.FM.040 (silent-null-parser):** там null теряется парсером; здесь null **легитимен**, но создаёт latent regression при schema change.
- **DP.FM.041 (dedup-slice-false-positive):** там dedup ложно совпадает; здесь dedup отсутствует когда должен быть.
