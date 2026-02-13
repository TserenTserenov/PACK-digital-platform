---
id: DP.SOTA.003
name: Open API Specifications
type: sota
status: active
summary: "Экосистема открытых спецификаций интерфейсов: OpenAPI (sync), AsyncAPI (event-driven), CloudEvents (envelope) + Arazzo (workflows)"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 4
  G: external
  R: 0.9
related:
  integrates_with: [DP.SOTA.001, DP.SOTA.006]
  enables: [DP.SYS.001]
tags: [openapi, asyncapi, cloudevents, arazzo, contracts, api, specifications]
---

# Open API Specifications

## 1. Определение

**Open API Specifications** — экосистема комплементарных открытых спецификаций для описания интерфейсов: OpenAPI (синхронные API), AsyncAPI (event-driven API), CloudEvents (формат событий), Arazzo (workflow chaining). Все четыре дополняют друг друга, не конкурируют.

## 2. Статус SOTA (февраль 2026)

**SOTA, зрелые и расширяющиеся.**

| Спецификация | Версия | Статус |
|-------------|--------|--------|
| OpenAPI | 3.2.0 (v4.0 Moonwalk в планах) | Industry standard |
| AsyncAPI | Linux Foundation member | Standard для event-driven |
| CloudEvents | CNCF graduated (Jan 2024) | Standard для событий |
| Arazzo | 1.0.1 | Новый: workflow chaining для агентов |

**Новое:** Arazzo — прямой ответ на agentic AI: описание цепочек API-вызовов как workflow.

## 3. SPF-интеграция

| SPF-слот | Что даёт |
|----------|----------|
| Boundary Statements | Machine-readable контракты между системами |
| Интеграция контекстов | Context Mapping через API specs |
| Evidence / Assurance | Контрактное тестирование по спецификации |
| Детерминированные системы | Формальное описание MCP-серверов и сервисов |

## 4. Правила применения

1. **При проектировании MCP-сервера** — описывать tools через формат, совместимый с OpenAPI.
2. **При интеграции агентов** — использовать Arazzo для описания multi-step workflows.
3. **При event-driven архитектуре** — AsyncAPI для описания интерфейса, CloudEvents для формата payload.
4. **Не подменять спецификации документацией** — spec = machine-readable contract, doc = human-readable explanation.

## 5. Источники

- OpenAPI Initiative: spec.openapis.org
- AsyncAPI: asyncapi.com (Linux Foundation)
- CloudEvents: cloudevents.io (CNCF graduated)
- Arazzo: spec 1.0.1 (OpenAPI Initiative)
