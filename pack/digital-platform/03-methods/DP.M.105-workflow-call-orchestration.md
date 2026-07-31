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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Единый entry point ↔ гибкость разных типов PR | Orchestrator — единственная точка входа для content/code/infra PR: управление упрощается, но изменение entry-логики затрагивает все типы PR одновременно |
| Устранение дублирования шагов ↔ косвенность исполнения | Каждая concern правится в одном reusable sub-workflow вместо N файлов, но лог конкретной проверки живёт в отдельном job — aggregate view приходится собирать отдельным metrics-collector'ом в step summaries |

## Алгоритм

1. Определить concerns (типы проверок/артефактов) — каждый в отдельный reusable sub-workflow
2. Создать orchestrator-workflow, который вызывает concerns через reusable call (workflow_call в GitHub Actions / include:project в GitLab CI)
3. Orchestrator собирает результаты и публикует aggregate step summary
4. Опционально: metrics-collector агрегирует CI run stats → step summaries видны без внешнего дашборда

## Три признака применимости

- Content pipeline с 2+ независимыми типами валидации
- Метрики нужны без внешнего дашборда (step summaries достаточно)
- Один entry point для разных типов PR (content / code / infra)

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Красота декомпозиции затмевает читаемость диагностики | Внимание смещается к дроблению pipeline на всё более мелкие reusable workflows, а разбор падения требует прыжков по N job-логам — aggregate summary, ради которого затевалась оркестрация, остаётся недонастроенным |
| Оркестратор воспринимается как универсальный ответ | Внимание фиксируется на самом паттерне, и признак применимости «2+ независимых типа валидации» перестаёт проверяться — workflow_call тащится в pipeline с одной проверкой, где добавляет только косвенность |

## Источник

DS-principles-curriculum WP-322 Ф8 — cd-pipeline.yml с lint-job + content-validation + metrics через workflow_call. Ф7: ci-metrics-dashboard.py + ci-metrics.yml + step summaries.

---

> 2026-07-31 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 4). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
