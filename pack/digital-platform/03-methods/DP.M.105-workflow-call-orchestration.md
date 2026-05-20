---
id: DP.M.105
name: "workflow_call orchestration: единый entry point с разделёнными concerns в CI/CD"
type: method
status: active
created: 2026-05-19
valid_from: 2026-05-19
sources:
  - DS-principles-curriculum commit 0c69230, WP-322 Ф8 (2026-05-19)
related:
  references: [DP.M.088, DP.M.093]
---

# DP.M.105 — Orchestration через workflow_call: единый entry point для CI/CD пайплайна

## Определение

Паттерн CI/CD pipeline: один orchestrator-workflow вызывает N специализированных reusable sub-workflows. Каждый реализует одну concern (lint, validation, metrics). Orchestrator = единственный entry point.

## Проблема

При добавлении N типов валидации шаги дублируются в каждом workflow-файле. Изменение одного шага = N одновременных правок. Метрики рассыпаются по N job-логам без aggregate view.

## Алгоритм

1. Определить concerns (типы проверок/артефактов) — каждый в отдельный reusable sub-workflow
2. Создать orchestrator-workflow, который вызывает concerns через reusable call (workflow_call в GitHub Actions / include:project в GitLab CI)
3. Orchestrator собирает результаты и публикует aggregate step summary
4. Опционально: metrics-collector агрегирует CI run stats → step summaries видны без внешнего дашборда

## Три признака применимости

- Content pipeline с 2+ независимыми типами валидации
- Метрики нужны без внешнего дашборда (step summaries достаточно)
- Один entry point для разных типов PR (content / code / infra)

## Источник

DS-principles-curriculum WP-322 Ф8 — cd-pipeline.yml с lint-job + content-validation + metrics через workflow_call. Ф7: ci-metrics-dashboard.py + ci-metrics.yml + step summaries.
