---
id: DP.M.182
title: "Dual SLA: Acknowledgment ≠ Completion"
type: method
status: draft
domain: digital-platform
valid_from: 2026-05-27
source: WP-358, DP.SC.162 v2
---

# DP.M.182 — Dual SLA: Acknowledgment ≠ Completion

## Суть

Разделение SLA async-сервиса на два независимых контракта:

1. **Acknowledgment SLA**: P95 ≤10с — подтверждение, что запрос принят (до запуска LLM)
2. **Completion SLA**: P95 ≤45с для light-запросов / async с нотификациями каждые 15с для heavy

## Инвариант

Тихий fail запрещён. Acknowledgment реализуется немедленно, до начала обработки.

## Применимость

Любые сервисы с нетривиальным временем обработки: боты, API, агентные пайплайны.

## Связи

- SC: DP.SC.162 (External Session Request)
- Различение: Session light ≠ heavy (distinctions.md)
