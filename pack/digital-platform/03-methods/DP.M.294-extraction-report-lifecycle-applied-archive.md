---
id: DP.M.294
type: method
title: "Lifecycle extraction-report: applied_commit + архивация после применения"
slug: extraction-report-lifecycle-applied-archive
domain: digital-platform
trust: high
epistemic_stage: observed
valid_from: 2026-06-07
source: peer-session 2026-06-07-03, extraction-reports id-deconflict; sessions/2026-06/2026-06-07-03-extraction-reports-id-deconflict/report.md
related: []
schema_version: 1
---

# DP.M.294 — Lifecycle extraction-report: applied_commit + архивация

## Описание

Метод финализации extraction-report после записи кандидатов в Pack-репо.

## Шаги

1. Добавить в frontmatter: `applied_commit: <SHA>` и обновить `status: applied`.
2. Переместить файл из `inbox/extraction-reports/` в `archive/extraction-reports/`.
3. Зафиксировать перемещение коммитом.

## Зачем

Без архивации inbox накапливает «применённые» файлы без маркера. Следующий прогон inbox-check не отличает их от новых кандидатов → ложная очередь и repeated processing. Аналог: PR merge → branch delete.

## Тест применимости

«Extraction-report находится в inbox/ и status = applied?» → применить lifecycle-метод.

## Связи

- Источник: DP.SC.004 (SLA inbox-check) — применённые файлы не должны засорять очередь
- Экстрактор: DP.AISYS.013
