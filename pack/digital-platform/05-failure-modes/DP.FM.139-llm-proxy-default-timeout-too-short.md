---
id: DP.FM.139
type: failure-mode
title: "Дефолтный таймаут LLM-прокси меньше времени генерации сложных ответов"
slug: llm-proxy-default-timeout-too-short
domain: digital-platform
trust: high
epistemic_stage: observed
valid_from: 2026-06-06
source: session-transcript 2026-06-06, WP-392 Б1; commit 81a8f997 (gateway-mcp wrangler.toml)
related: [DP.IWE.003]
schema_version: 1
---

# DP.FM.139 — Дефолтный таймаут LLM-прокси меньше времени генерации сложных ответов

## Описание

LLM-прокси (CF Worker, Lambda, Railway) настроен с дефолтным таймаутом, оптимизированным под API-запросы (25с). Сложные LLM-генерации (ASCII-арт, HTML, диаграммы) занимают >25с → прокси завершает соединение до получения ответа → клиент видит 524 (CF) или эквивалентную timeout-ошибку.

## Симптом

Простые запросы («Привет») проходят. Сложные («нарисуй схему», «генерируй HTML») зависают или возвращают timeout-ошибку без сообщения об ошибке.

## Порождающая причина

Дефолтный таймаут рассчитан на короткий synchronous API-ответ, не на streaming LLM-генерацию. При добавлении LLM-агента в цепочку общий latency = DB + validation + LLM-generation, где LLM-generation непредсказуема по длительности.

## Митигации

1. Поднять таймаут прокси до 60000мс или выше (для CF Workers: `HERMES_RUNTIME_TIMEOUT=60000` в wrangler.toml).
2. Два уровня таймаута: транспортный (`GATEWAY_MCP_TIMEOUT` на клиенте) + runtime (`HERMES_RUNTIME_TIMEOUT` на прокси) — оба нужны независимо.
3. Streaming-ответ + прогресс-нотификации: клиент получает чанки → таймаут не применяется к первому байту.

## Тест

«Простые запросы OK, сложные timeout?» + «Timeout появился при добавлении LLM в цепочку?» → DP.FM.139.

## Связи

- DP.IWE.003 (Cloud Gateway): gateway-специфичная конфигурация
- DP.FM.137 (асимметричная suppression): смежный класс монопутевых ошибок инфраструктуры
