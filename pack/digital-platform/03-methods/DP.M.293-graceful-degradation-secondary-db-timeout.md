---
id: DP.M.293
type: method
title: "Graceful degradation чтения вторичной БД: таймаут + null-fallback"
slug: graceful-degradation-secondary-db-timeout
domain: digital-platform
trust: high
epistemic_stage: observed
valid_from: 2026-06-07
source: session-transcript 2026-06-07, WP-349 Ф26; commit DS-MCP buildJourneyState (LEARNING_DATABASE_URL)
related: []
schema_version: 1
---

# DP.M.293 — Graceful degradation чтения вторичной БД: таймаут + null-fallback

## Описание

Метод устойчивого чтения из вторичного источника данных внутри обработчика основного запроса: операция ограничена явным таймаутом, при отказе возвращает null-значения без прерывания основного ответа.

## Шаги

1. Запустить чтение вторичной БД с явным таймаутом (AbortController / asyncio.wait_for / Promise.race / любой механизм прерывания).
2. При таймауте или ошибке — вернуть null/default-значения без raise/throw.
3. Основной запрос завершается в любом случае.
4. **Опционально:** если primary поле ещё null — подставить из secondary (дешёвое обогащение ответа).

## Тест применимости

«Вторичный источник данных может быть недоступен независимо от основного?» + «Основной ответ должен выдаваться даже при отказе вторичного?» → применить метод.

## Антипаттерн

`await secondary_read()` без таймаута = основной запрос падает вместе с вторичным.

## Связи

- Применяется в: WP-349 Ф26 (LEARNING_DATABASE_URL через buildJourneyState)
