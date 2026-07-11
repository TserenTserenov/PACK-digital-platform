---
id: DP.FM.264
type: failure-mode
title: housekeeping-семафор без поля slug — note-file --slug не находит семафор при параллельной сессии
trust: observed
epistemic_stage: confirmed
domains: [session-guard, semaphore, parallel-agents, iwe-runtime]
source_session: 2026-07-07 session-close (git diff DS-my-strategy commit 6fa45b19e, session-guard gap)
valid_from: 2026-07-07
schema_version: 1
related:
  see_also: [DP.FM.215]
---

# DP.FM.264 — housekeeping-семафор без slug → слепое пятно при параллельной сессии

## Симптом

session-guard в `--housekeeping` режиме создаёт файл-семафор без поля `slug:`. При параллельно запущенной обычной сессии, которая вызывает `note-file --slug <name>`, поиск по slug не находит housekeeping-семафор → сценарий параллельной работы ломается: lock не виден, housekeeping-guard игнорируется.

## Корень

housekeeping-режим не генерирует синтетический slug при создании семафора. Lookup по slug предполагает, что любой активный семафор имеет поле `slug:`.

## Профилактика

**Правило:** семафор любого режима обязан писать поле `slug:`. Если режим не предполагает явного имени — генерировать синтетический: `housekeeping-YYYY-MM-DD-HH`.

Тест: `grep -rL "slug:" ~/.iwe-sessions/*.ptr 2>/dev/null` — вывод должен быть пустым (все ptr-файлы содержат slug).

**Обход до фикса скрипта (S-33):** использовать обычную сессию с уникальным `--slug` вместо `--housekeeping`.

## Применимо к

- session-guard.sh в IWE runtime
- Любые другие режимы-указатели, создающие семафор без полного набора полей

## Связано

- DP.FM.215 — смежный класс: семафор ключуется по agent-id, не session-id (гонка при close); этот FM — отсутствие slug-поля (lookup failure). Разные root cause.
