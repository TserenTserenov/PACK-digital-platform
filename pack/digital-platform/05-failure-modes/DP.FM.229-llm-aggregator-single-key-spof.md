---
id: DP.FM.229
name: "LLM aggregator single-key SPOF: model-fallback список не защищает от ключа с limit"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-15
source: "WP-244 action-report.md §P0 (Claude API 403), session-close 2026-06-15"
related:
  references: [DP.FM.153]
tags: [llm, api-key, credentials, fallback, resilience, openrouter, aggregator, spof]
---

# DP.FM.229 — LLM aggregator single-key SPOF: model-fallback список не защищает от ключа с limit

## Паттерн

LiteLLM / LLM-proxy настроен с fallback-списком моделей (`claude-sonnet → kimi-k2 → gpt-4o-mini`) через один агрегатор-прокси (OpenRouter). Все модели используют один API-ключ. Ключ упирается в **total spending limit** → 403 для всего пула моделей одновременно.

**Сигнал:** все модели из fallback-списка возвращают 403 / AuthorizationError в одно время, начиная с одного момента.

## Последствие

- Fallback-список выглядит как диверсификация риска, но не защищает от key-level failures
- Total spending limit (OpenRouter) = глобальный блок всего трафика через ключ, независимо от модели
- Дефект невидим в штатном режиме: fallback по per-model failures работает корректно, ложного чувства защиты нет до момента hit лимита
- Обнаружение — через downstream-эффекты (ошибки в боте, пустые отчёты), а не через прямой алерт

## Корневая причина

Оси диверсификации не ортогональны:

| Ось | Что покрывает fallback-список | Что НЕ покрывает |
|-----|-------------------------------|------------------|
| **Model axis** | per-model rate limit, 5xx у провайдера | — |
| **Credential axis** | — | total spending limit, ключ отозван, агрегатор заблокировал аккаунт |

Конфиг вида `models: [model_A, model_B, model_C]` диверсифицирует только по model-axis; credential-axis остаётся единой точкой отказа, если все модели идут через один ключ/агрегатор.

## Правило

Для критических LLM-приложений резервный fallback обязан диверсифицировать **credential-axis**, а не только model-axis:

- **Вариант A:** несколько ключей к одному агрегатору (разные daily/total лимиты)
- **Вариант B:** прямые ключи к провайдерам (Anthropic, OpenAI) в дополнение к агрегатору — полная credential-диверсификация

## Тест обнаружения

«Если ключ №1 упадёт целиком (403/limit) — конфиг fallback-моделей переключит запрос на другой ключ или останется на том же?»

- Остаётся на том же → credential SPOF — этот FM
- Переключает ключ → credential resilience достигнута

## Профилактика

1. В LiteLLM: `fallbacks` конфигурировать по разным `api_key` / `api_base`, не только по `model`
2. Мониторинг `total_spent / limit_remaining` через агрегатор API — алерт при 80% лимита
3. Ротация ключей: два активных ключа с независимыми лимитами (A/B pattern)
4. Smoke-test: прямая проверка `/v1/chat/completions` по каждому ключу из fallback-цепочки — отдельный канал, не через app

## Связи

- **Родственный FM:** [DP.FM.153](DP.FM.153-intermittent-401-static-key-proxy-or-env.md) — 401 по статическому ключу/env (другой класс: прерывистый, не total-limit)
- **Родственное различение в 01B-distinctions.md:** «Явный policy-deny (fail-closed) ≠ infra-сбой стража (fail-open-with-alarm)» — смежный класс single-point-of-failure для authz
- **Родственное различение в 01B-distinctions.md:** «Внутренний алерт (failure) ≠ Внешний heartbeat (dead-man's switch)» — обнаружение downstream vs прямой probe
- **Источник:** WP-244 action-report.md §P0, session-close 2026-06-15
