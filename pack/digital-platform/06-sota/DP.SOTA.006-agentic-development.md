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
  operationalized_by: [DP.AISYS.013, DP.ROLE.012]
  enables: [DP.ROLE.001]
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
| Стратег (DP.ROLE.012) | Планирование (day/week/month) | Governance, расписание |
| Экстрактор (DP.AISYS.013) | Знания (detect→classify→formalize) | Knowledge engineering |
| Синхронизатор | Проекция (Pack→Downstream) | Автоматизация |
| Бот (DP.AISYS.014) | Интерфейс пользователя | Surface |

## 5. Правила применения

1. **При добавлении функциональности** — сначала спросить: какой агент должен это делать? Не класть всё в одного.
2. **При проектировании агента** — определить: BC, IPO, контракты с другими агентами.
3. **При оркестрации** — агенты работают параллельно, не последовательно, если задачи независимы.
4. **Не делегировать 100%** — человек остаётся в петле (human-in-the-loop) для валидации.

## 5.1. Паттерн: Мультиагентный QA (Context Isolation)

**Суть:** Создатель (сильная модель, Opus) производит артефакт, зная весь контекст. Ревьюер (слабая модель, Haiku) видит только финальный результат — без контекста создания. Изоляция контекста спасает от слепых пятен автора.

| | Создатель | Ревьюер |
|---|---|---|
| Модель | Opus/Sonnet (сильная) | Haiku (слабая) |
| Контекст | Полный (история, решения, trade-offs) | Только результат + Pack (source-of-truth) |
| Задача | Создать артефакт | Найти несоответствия, логические дыры |
| Bias | Confirmation bias (видит то, что хотел сделать) | Нет bias (не знает намерений) |

**Принцип:** Один ИИ — усилитель. Два ИИ с изоляцией контекста — система контроля качества. Аналог: Code Review в разработке.

**Реализация в IWE:** Чеклист-верификация (CLAUDE.md §2.4) — sub-agent Haiku R23 проверяет формальное соответствие чеклисту Quick Close и Day Close.

**Ограничение:** Ревьюер проверяет формальное соответствие, не качество содержания. Для содержательной проверки нужен эксперт (human gate, DP.D.046).

## 6. Production Coordination Metrics (2026)

> Обогащение: Scout 26 мар 2026 (IBM Research, LangGraph, Kanerika)

**Coordination challenge:** interaction count grows exponentially with agent count → coordination cost explodes.

| Метрика | Значение | Источник |
|---------|----------|---------|
| Process hand-offs | **-45%** с intelligent routing | IBM Research |
| Decision speed | **3x improvement** | IBM Research |
| Multi-agent unmanageable threshold | **>5 agents** (75% систем) | LangGraph 2026 |
| Pilot-to-production success rate | **10-15%** | Iterathon 2026 |

**Mitigation strategies:**
- Team size limits: 3-7 agents per workflow, hierarchical beyond that
- Centralized (supervisor) vs decentralized — trade-off: clear control vs flexibility
- Intelligent routing: different models for different complexity (cost optimization)

> Подробнее: AS.SOTA.003 (governance), AS.FM.015 (communication failures)

## 7. Источники

- Anthropic. «2026 Agentic Coding Trends Report» (2026)
- The New Stack. «5 Key Trends Shaping Agentic Development in 2026» (2026)
- IBM Research. Multi-agent coordination metrics (2026)
- Iterathon. «Agent Orchestration 2026» (2026)
