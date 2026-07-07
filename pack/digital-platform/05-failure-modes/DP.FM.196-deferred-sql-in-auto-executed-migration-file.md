---
id: DP.FM.196
title: "Deferred SQL в auto-executed migration file: dormant-конструкция применяется автоматически в первую же ночь"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / data-platform
epistemic_stage: confirmed
valid_from: 2026-07-05
source: "session-close 2026-07-05 (WP-417, panel_store.py — ensure_schema)"
related:
  see_also: [DP.FM.060]
tags: [migration, schema, postgresql, rls, deferred, ensure_schema, atomicity]
wp: WP-417
---

# DP.FM.196 — Deferred SQL в auto-executed migration file: dormant-конструкция применяется автоматически в первую же ночь

## Описание

Функция `ensure_schema()` (или аналогичный идемпотентный bootstrap) читает единый migration-файл и исполняет его целиком за одну транзакцию. Любая «спящая» (dormant) конструкция, добавленная в тот же файл с расчётом «применить позже вручную», активируется автоматически при первом запуске фонового обработчика.

## Пример

WP-417: в `panel_store.py` функция `ensure_schema()` вызывается каждый запуск фонового worker'а. Попытка добавить «dormant» RLS-политику в тот же migration-файл — политика применилась бы в первую же ночь без отдельного решения о включении.

## Тест обнаружения

«Функция auto-execute при каждом запуске читает целый migration-файл?» Да → добавление любой dormant-конструкции в этот файл = немедленная активация.

## Инвариант

`ensure_schema()` не поддерживает «применить только часть файла». Всё, что в файле — применяется атомарно.

## Митигация

1. Deferred-конструкции (политики, функции-стражи, правила с отложенным вводом) помещать в **отдельный файл**, не подключённый к auto-execution.
2. Отдельный файл активируется явной ручной командой при готовности.
3. Принцип: один файл = один уровень готовности (auto vs manual).

## Связи

- DP.FM.060 (Half-Migration Manifest-Runner Split) — смежный паттерн: там split runner/manifest; здесь split auto/manual execution
- WP-417 — первый прецедент
