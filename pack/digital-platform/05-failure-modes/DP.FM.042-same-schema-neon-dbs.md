---
id: DP.FM.042
title: "Одноимённая схема в двух Neon-БД: скрытый дрейф данных"
type: failure-mode
pack: PACK-digital-platform
domain: digital-platform
tags: [neon, schema-naming, drift, multi-db, metabase, silent-fail]
valid_from: 2026-05-16
status: draft
schema_version: 1
---

# DP.FM.042 — Одноимённая схема в двух Neon-БД: скрытый дрейф данных

## Описание

В архитектуре с несколькими Neon-БД одна и та же схема (например, `health`) может присутствовать как standalone-БД (`health.public.*`) и как non-public схема в другой БД (`learning.health.*`). Инструмент, созданный «по аналогии», читает из пустого источника без ошибок.

## Проявление

Метрики всегда NULL или пусты. Ошибок нет. Запись продолжается.

Конкретный случай: Metabase-карта Card 4.1 читала из `health.public.internal_metrics` (пустая standalone-БД), тогда как `write_internal_metric` пишет в `learning.health.internal_metrics`. Детектирован VR.R.001 (CONDITIONAL → PASS).

## Корневая причина

Создание инструмента «по аналогии» без явного указания полного квалификатора `<db>.<schema>.<table>`. При нескольких БД с одноимёнными схемами — высокая вероятность попасть не туда.

## Профилактика

1. При создании Metabase-карты или SQL-запроса: указывать полный квалификатор `<db_name>.<schema_name>.<table_name>`
2. Сразу после создания: `SELECT COUNT(*) FROM <таблица>` — если 0, данные пишутся в другое место
3. Проверить, в какую БД реально пишет writer (grep по `psycopg2.connect`, переменным `DATABASE_URL`)

## Источник

WP-310 session-transcript 2026-05-16 + git diff DS-my-strategy (12249779).
