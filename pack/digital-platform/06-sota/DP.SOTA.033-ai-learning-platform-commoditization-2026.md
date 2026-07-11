---
id: DP.SOTA.033
title: "Коммодитизация слоёв AI Learning Platform (2026): защита через содержание за шлюзом"
type: sota
domain: digital-platform
status: draft
valid_from: 2026-07-02
fpf_parent: U.Method
sources:
  - "competitive-analysis-platform-iwe.md, 12-категорийный анализ ~50 продуктов, commit ce91c2ee2"
  - "session-transcript 2026-07-02 (WP-459 конкурентный анализ)"
applicability:
  - product-strategy / competitive-positioning
  - platform-architecture / feature-investment-decisions
related:
  see_also: [DP.SOTA.030, DP.D.036]
tags: [commoditization, mcp, memory, ai-learning-platform, competitive-moat, 2026]
---

# DP.SOTA.033 — Коммодитизация слоёв AI Learning Platform (2026)

## Феномен

По состоянию на 2026 год три технических «козыря» AI Learning Platform — MCP-подключение,
память о пользователе и сократический учебный режим — коммодитизировались или
коммодитизируются в течение одного релизного цикла.

**Коммодитизированные слои:**

| Слой | Статус 2026 | Кто предлагает |
|------|-------------|----------------|
| MCP-мультиклиентность | Отраслевой стандарт | Anthropic, OpenAI, Google |
| Память о пользователе | $0–20/мес | Anthropic Memory, OpenAI Memory, Gemini |
| Сократические учебные режимы | Бесплатно | Все крупные LLM-вендоры |

**Угроза консолидации памяти BigTech (тренд):**
Слой личного контекста уходит в ОС: Siri AI на Gemini, Windows Recall, ChatGPT Dreaming.
Консолидация: Bee→Amazon, Limitless→Meta. Сбор личного контекста обесценивается как
конкурентное преимущество.

## Значение для платформы

Уникальность продукта держится на **содержании за шлюзом**, не на способе подключения.

Защищаемая связка (не один элемент, а комбинация):
- методология как источник нормы (Pack-знание)
- данные реальной работы пользователя
- модель ступени развития (Digital Twin)
- владение данными пользователем (BYOB-архитектура)

## Тест для инвестиционных решений

«Этот feature войдёт в стандарт или будет бесплатно у конкурентов через 12 месяцев?»
Да → не строить как дифференциатор; строить как commodity-инфраструктуру (дёшево).
Нет → инвестировать как в источник защищаемого преимущества.

## Связи

- [DP.D.036](../01-domain-contract/01B-distinctions.md) — BYOB Knowledge Architecture
- [DP.SOTA.030](DP.SOTA.030-eam-agent-manifest-standard.md) — EAM: стандартизация описания агентов (ещё одна коммодитизация)
- [DP.D.236](../01-domain-contract/01B-distinctions.md) — доставка обучения к работе ≠ производственное действие как шаг развития
