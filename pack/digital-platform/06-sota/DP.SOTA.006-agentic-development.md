---
id: DP.SOTA.006
name: Agentic Development
type: sota
status: active
summary: "Multi-agent orchestration: инженеры оркестрируют специализированных агентов, а не пишут код напрямую; requirement-to-deploy за часы"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 4
  G: external
  R: 0.7
related:
  integrates_with: [DP.SOTA.001, DP.SOTA.002, DP.SOTA.005]
  operationalized_by: [DP.AISYS.013, DP.AGENT.012]
  enables: [DP.AGENT.001]
tags: [agentic, multi-agent, orchestration, anthropic, claude-code, development]
---

# Agentic Development

## 1. Определение

**Agentic Development** — парадигма разработки, где инженеры оркестрируют специализированных ИИ-агентов, каждый с выделенным контекстом и инструментами, вместо написания кода напрямую. Агенты работают параллельно, каждый в своём bounded context.

## 2. Статус SOTA (февраль 2026)

**SOTA, defining trend.**

- **Anthropic «2026 Agentic Coding Trends Report»:** 8 трендов. Core shift: разработчики используют AI в ~60% работы, но полностью делегируют только 0-20%.
- **Multi-agent coordination** с параллельными специализированными агентами — emerging architecture pattern.
- **Workflow compression:** requirement-to-deploy сокращается с недель/месяцев до часов/дней.

## 3. SPF-интеграция

| SPF-слот | Что даёт |
|----------|----------|
| Bounded Context | Каждый агент = отдельный BC с собственным контекстом |
| IPO-паттерн | Каждый агент = Input → Process → Output |
| Context Mapping | Inter-agent integration через контракты |
| Отчуждаемость | Platform-space агенты работают для любого пользователя |

## 4. Реализация в экзокортексе

| Агент | Bounded Context | Роль |
|-------|----------------|------|
| Claude Code | Сессия работы (Open→Work→Close) | Оркестратор, исполнитель |
| Стратег (DP.AGENT.012) | Планирование (day/week/month) | Governance, расписание |
| Экстрактор (DP.AISYS.013) | Знания (detect→classify→formalize) | Knowledge engineering |
| Синхронизатор | Проекция (Pack→Downstream) | Автоматизация |
| Бот (DP.AISYS.014) | Интерфейс пользователя | Surface |

## 5. Правила применения

1. **При добавлении функциональности** — сначала спросить: какой агент должен это делать? Не класть всё в одного.
2. **При проектировании агента** — определить: BC, IPO, контракты с другими агентами.
3. **При оркестрации** — агенты работают параллельно, не последовательно, если задачи независимы.
4. **Не делегировать 100%** — человек остаётся в петле (human-in-the-loop) для валидации.

## 6. Источники

- Anthropic. «2026 Agentic Coding Trends Report» (2026)
- The New Stack. «5 Key Trends Shaping Agentic Development in 2026» (2026)
