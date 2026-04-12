---
id: DP.SOTA.014
name: "MCP как де-факто стандарт 2026"
type: sota
status: active
summary: "Model Context Protocol — универсальный стандарт подключения AI-агентов к enterprise-инструментам. 97M+ скачиваний SDK, 75+ коннекторов"
created: 2026-03-21
edition: "2026-03"
trust:
  F: 4
  G: external
  R: 0.9
related:
  enables: [DP.EXOCORTEX.001, DP.ARCH.001]
  integrates_with: [DP.SOTA.002]
tags: [mcp, ai-agents, integration, standards]
---

# MCP как де-факто стандарт 2026

## 1. Определение

**Model Context Protocol (MCP)** — открытый протокол для подключения AI-агентов к внешним инструментам, данным и сервисам. Разработан Anthropic, стал де-факто универсальным стандартом в 2026.

## 2. Статус SOTA (март 2026)

**SOTA, консенсус.**

- 97M+ скачиваний SDK
- 75+ готовых коннекторов (GitHub, Slack, Linear, PostgreSQL, Google и др.)
- Поддержка: Anthropic, OpenAI, Google, Microsoft, AWS, Cloudflare
- Adoption: от индивидуальных разработчиков до enterprise (Shopify, Block, Replit)

## 3. Значение для платформы

| Аспект | Влияние |
|--------|---------|
| **Интеграция** | Единый протокол для всех MCP-серверов экзокортекса (knowledge-mcp, calendar, gmail и др.) |
| **Переносимость** | MCP-сервер работает с любым AI-клиентом (Claude, ChatGPT, Cursor) |
| **Масштабируемость** | Новая интеграция = новый MCP-сервер, без изменения агента |
| **Context Engineering** | MCP = реализация стратегий Select и Isolate из DP.SOTA.002 |

## 4. Revision criterion

Пересмотреть при: (1) появлении конкурирующего протокола с >50% adoption, (2) существенном изменении спецификации MCP, несовместимом с текущей архитектурой.

## 5. 2026 Roadmap Priorities (обновление: Scout 29 мар 2026)

> Источники: MCP Blog 2026 Roadmap, The New Stack, Use Apify MCP 2026

**4 приоритета core maintainers:**

| # | Приоритет | Описание | Влияние на IWE |
|---|-----------|----------|----------------|
| 1 | **Transport scalability** | Stateful sessions × load balancers problem, horizontal scaling workarounds → streamable HTTP improvements | WP-187 (Ory MCP integration) |
| 2 | **Agent communication** | Longer-running work: agents trigger multi-day tasks | WP-171 (Activity Hub multi-day orchestration) |
| 3 | **Governance maturation** | Audit trails, SSO, gateway behavior, config portability для enterprise | R2 Архитектура — governance layer |
| 4 | **Enterprise readiness** | 1000+ connectors, major vendors adopted | Экосистема MCP-серверов IWE |

**Market:** $1.8B (2025). All major vendors adopted (OpenAI, Anthropic, Google, Amazon).

**Clarification:** MCP = **open specification** with growing adoption, NOT ISO/IEC standard.

## 6. Официальный Roadmap 2026 (обновление: Scout 12 апр 2026)

> Источник: https://blog.modelcontextprotocol.io/posts/2026-mcp-roadmap/

**Приоритет 1: Transport scalability**
Streamable HTTP → stateless multi-instance операция. Устраняет конфликты с load balancers при горизонтальном масштабировании MCP серверов. Критично для production Gateway deployments.

**Приоритет 2: Enterprise readiness**
Audit trails, SSO auth, gateway behavior, configuration portability.

**Приоритет 3: Agent communication semantics**
Task retry semantics, expiry policies для long-running agent tasks.

**Adoption (апрель 2026):** OpenAI, Microsoft, Google, Amazon — все приняли MCP.
Lead Maintainer: Den Delimarsky. Core: Clare Liguori добавлена.

**Импликация для IWE:**
- Stateless transport снимает current limitation Gateway MCP при scaling (WP-187)
- Enterprise auth = foundation для платного доступа через MCP (R9 Knowledge Gateway)

## 7. Связанные документы

- [DP.SOTA.002](./DP.SOTA.002-context-engineering.md) — Context Engineering (MCP реализует Select/Isolate)
- [DP.EXOCORTEX.001](../02-domain-entities/DP.EXOCORTEX.001-modular-exocortex.md) — модульный экзокортекс
- Источник: Scout 21 мар 2026
