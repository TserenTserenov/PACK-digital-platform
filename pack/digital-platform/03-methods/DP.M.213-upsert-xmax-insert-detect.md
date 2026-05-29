---
id: DP.M.213
name: "UPSERT + xmax=0 — атомарное определение INSERT vs UPDATE"
type: method
domain: digital-platform
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-28
schema_version: 1
source: "session-transcript 2026-05-28, WP-330 Ф8.2 P0-1.5 TOCTOU fix"
---

# DP.M.213 UPSERT + xmax=0: атомарное определение INSERT vs UPDATE

## Описание

PostgreSQL idiom для атомарного определения того, была ли строка только что создана (INSERT) или уже существовала (UPDATE), за один roundtrip без гонки состояний (TOCTOU).

## IPO

**Вход:** данные для upsert + уникальный ключ конфликта
**Процесс:** `INSERT ... ON CONFLICT DO UPDATE SET ... RETURNING (xmax = 0) AS is_new`
**Выход:** `bool` — True = INSERT (новая строка), False = UPDATE (строка существовала)

## Механизм

`xmax = 0` в PostgreSQL означает, что строка не имеет активной блокирующей транзакции и была только что INSERT, а не UPDATE. После UPDATE xmax содержит ID транзакции, которая изменила строку.

## Тест применимости

«Нужно ли знать INSERT vs UPDATE без гонки (TOCTOU)?» — Да → применять DP.M.213.

## Пример (Python/asyncpg)

```sql
INSERT INTO checkins (user_id, date, data)
VALUES ($1, $2, $3)
ON CONFLICT (user_id, date) DO UPDATE
  SET data = EXCLUDED.data
RETURNING (xmax = 0) AS is_new;
```

## Применение

- Идемпотентный чек-ин (marathon, daily activity)
- Дедупликация webhook-событий
- Любой сценарий: «нужно знать, первый ли раз»

## Связи

- Источник: WP-330 marathon TOCTOU fix, 2026-05-28
