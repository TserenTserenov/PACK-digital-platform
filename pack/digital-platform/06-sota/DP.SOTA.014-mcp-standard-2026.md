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

## 5. Связанные документы

- [DP.SOTA.002](./DP.SOTA.002-context-engineering.md) — Context Engineering (MCP реализует Select/Isolate)
- [DP.EXOCORTEX.001](../02-domain-entities/DP.EXOCORTEX.001-modular-exocortex.md) — модульный экзокортекс
- Источник: Scout 21 мар 2026
