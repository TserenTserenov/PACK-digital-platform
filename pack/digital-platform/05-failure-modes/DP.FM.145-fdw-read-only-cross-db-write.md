---
id: DP.FM.145
name: "FDW-только-READ: cross-DB write в SQL-миграции молча провалится"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: database-integration
severity: major
valid_from: 2026-06-08
related:
  see_also: []
tags: [fdw, foreign-data-wrapper, cross-db, migration, neon, psycopg2, sql, silent-failure]
source: "session 2026-06-08, git diff DS-IT-systems/neon-migrations (a985358, 259-stage-based-qual-and-bonuses.sql + 259c-backfill.py)"
schema_version: 1
---

# DP.FM.145 — FDW-только-READ: cross-DB write в SQL-миграции молча провалится

## Описание

Foreign Data Wrapper (FDW) даёт только READ-доступ к удалённой таблице. Любая попытка записи во внешнюю БД внутри SQL-файла миграции провалится с ошибкой прав (или молча, в зависимости от конфигурации).

**Особая коварность при блочных миграциях:** блоки A и B (локальные операции) применяются успешно, блок C (cross-DB write) падает уже после их применения — состояние базы остаётся полуобновлённым.

## Контекст возникновения

- Миграции с несколькими блоками операций, где один блок пишет в удалённую таблицу через FDW
- Системы с Neon FDW, RDS FDW или любым аналогом
- SQL-файлы, применяемые к одной БД, но ссылающиеся на таблицы другой БД

## Симптом

- Миграция завершается с ошибкой прав на блоке C после успешного применения блоков A, B
- Локальные таблицы изменены, удалённые — нет
- При повторном запуске миграции: ошибка «уже применено» на блоках A, B

## Паттерн защиты

Cross-DB backfill = отдельный Python-скрипт с двумя независимыми `psycopg2.connect()` вызовами:

```python
conn_local = psycopg2.connect(LOCAL_DB_URL)
conn_remote = psycopg2.connect(REMOTE_DB_URL)
# явный --dry-run флаг
```

Никогда не смешивать cross-DB write с SQL-миграционными файлами.

## Тест

«Есть ли в SQL-миграции INSERT/UPDATE в таблицу через FDW?» Да → рефакторить в отдельный Python-скрипт с двумя подключениями.
