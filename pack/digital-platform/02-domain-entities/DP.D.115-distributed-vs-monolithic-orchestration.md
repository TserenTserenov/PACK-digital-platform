---
id: DP.D.115
name: "Distributed orchestration ≠ Monolithic orchestrator"
type: distinction
domain: digital-platform
pack_refs: []
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "peer-сессия 2026-05-29-14-mim-as-it-company, Тема 3 «Оркестрация (K8s-аналог)»"
---

# DP.D.115 Distributed orchestration ≠ Monolithic orchestrator

## Различение

| | Monolithic orchestrator | Distributed orchestration |
|---|------------------------|---------------------------|
| **Природа** | Сущность (одна точка управления) | Свойство ансамбля компонентов |
| **Пример** | K8s control plane как монолит | launchd + process-catalog + evaluator-worker + file-lock + event-gateway |
| **Точка отказа** | Single (orchestrator падает — всё стоит) | Distributed (отказ одного убирает одну функцию) |
| **Mapping K8s-аналогий** | CronJob, HPA, Scheduler, Leader election, Service mesh — в одном control plane | Каждая роль реализована независимым инструментом |
| **Архитектурный вопрос** | «Где орочестратор?» | «Какие роли оркестрации и какими инструментами реализованы?» |

## Тест

«Можно ли убрать один компонент так, что система потеряет ровно одну функцию оркестрации?»
- Да → distributed
- Нет (одна точка отказа управляет всем) → monolithic

## Применение

При архитектурном анализе heterogeneous knowledge/agent системы — не искать «единый оркестратор» по аналогии с K8s. Перечислить роли оркестрации (scheduler / HPA / leader election / service mesh / cron) и маппить каждую на собственный инструмент. Mapping:

- **CronJob** ↔ launchd + process-catalog.yaml
- **HPA** ↔ stage_evaluator worker (scale на основе метрик)
- **Scheduler** ↔ WP-350 Маршрутизатор (executor-catalog)
- **Leader election** ↔ Local Gateway file-lock
- **Service mesh** ↔ event-gateway + projection-workers

## Антипаттерн

Искать «единый control plane» в системе, собранной из независимых инструментов → конструировать несуществующий компонент / переписывать систему под monolithic-паттерн без необходимости.
