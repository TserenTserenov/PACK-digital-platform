---
id: DP.SOTA.005
name: AI-Native Org Design
type: sota
status: active
summary: "Организационная архитектура для AI-first: плоские иерархии, agent orchestration, end-to-end accountability вместо функциональных силосов"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 3
  G: external
  R: 0.5
related:
  integrates_with: [DP.SOTA.006, DP.SOTA.002]
  enables: [DP.ARCH.001]
tags: [ai-native, organization, design, deloitte, wef, flat-hierarchy]
---

# AI-Native Org Design

## 1. Определение

**AI-Native Org Design** — принципы организационной архитектуры, оптимизированной для AI-first операций: плоские иерархии, оркестрация агентов вместо ручной координации, end-to-end accountability вместо функциональных силосов, AI architect как ключевая роль.

## 2. Статус SOTA (февраль 2026)

**SOTA, emerging → consolidating.**

- **Deloitte Tech Trends 2026:** «The Great Rebuild: Architecting an AI-Native Tech Organization» — крупнейшие компании перестраивают IT-функцию.
- **WEF (Jan 2026):** «Will the next decade of business quickly become AI-native?» — сдвиг от теории к реализации.
- **Структурные сдвиги:** 10-20% сокращение middle management к концу 2026, AI architect roles растут с 30% до 58%.
- **Anthropic (2026):** agentic coding выходит за пределы инженерных команд в organization-wide transformation.

## 3. SPF-интеграция

| SPF-слот | Что даёт |
|----------|----------|
| Генеративность (4-я характеристика) | AI-native org = org, которая генерирует ценность через агентов |
| Platform-space / User-space | Отчуждаемость платформы = шаблон AI-native организации |
| ИИ-системы (реестр) | Каждый агент = организационная единица с bounded context |

## 4. Правила применения

1. **При проектировании экосистемы** — каждый агент = организационная роль с определённой ответственностью, не просто код.
2. **При добавлении нового агента** — проверять: есть ли end-to-end accountability? Кто владелец результата?
3. **При масштабировании** — думать об организации агентов как об организации команд: каждый со своим bounded context, UL и контрактами.

## 5. Источники

- Deloitte. «Tech Trends 2026: The Great Rebuild» (2026)
- WEF. «AI-Native Business Decade» (Jan 2026)
- Anthropic. «Eight Trends Defining How Software Gets Built in 2026» (2026)
