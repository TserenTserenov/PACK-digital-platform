---
id: DP.SC.184
title: "Day Open через бота"
status: draft
version: "0.1"
created: 2026-06-19
wp: WP-428
depends_on:
  - adr-unified-bot-router-v1
tier_required: T3+
privacy_class: privacy-by-design
---

# DP.SC.184 «Day Open через бота»

## Обещание

Пользователь IWE может начать день через Telegram-бота без VS Code: получить краткий дайджест ритма, активных задач и одного фокуса дня.

## Триггеры

- Команда `/day`

## Входы

- `telegram_id` — идентифицирует пилота через UnifiedBotRouter (adr-unified-bot-router-v1)

## Pipeline (детерминированный)

```
1. Ory lookup → tier + pilot_id (через Router)
2. Загрузить ритм: последние 7 дней (баллы, слоты) из Aisystant MCP
3. Загрузить активные РП: get_journey_state → top-3 по приоритету
4. Фокус дня: первый pending из active WPs
5. LLM-синтез: короткий текст (≤200 chars) из пунктов 2-4
6. Отправить в Telegram
```

LLM используется ТОЛЬКО на шаге 5 (синтез текста). Основной pipeline (шаги 1-4) детерминированный.

## Privacy by design (ИНВАРИАНТ — аналогичен DP.SC.183)

В Telegram идёт только синтезированный дайджест — без raw атрибутов (cp-scores, WP-IDs по именам, числовые значения профиля).

**Non-regression smoke invariant:** те же PII-паттерны: `WP-\d+`, `cp\.\w+=`, `@\w+\.`

## SLA

- P95 ≤60с (всегда heavy: pipeline + LLM синтез)
- Режим: async (typing action сразу после команды)

## Fallback

| Ситуация | Поведение |
|----------|-----------|
| Aisystant MCP недоступен | «Данные временно недоступны. Попробуй через 5 минут.» |
| Нет активных РП | Дайджест ритма без секции планов |
| Timeout (>60с) | Error envelope из adr-unified-bot-router-v1 |

## Свидетельства результата

- `bot.day_open.completed` domain event (без тела, только duration_ms)
- Пользователь получил дайджест с ≥1 пунктом ритма и ≥1 фокусом дня

## Smoke тест

```
1. Отправить /day в тестовый аккаунт T3+
2. Проверить ответ ≤60с
3. grep на PII-паттерны в ответе → 0 совпадений
4. Ответ содержит: упоминание ритма + хотя бы один фокус
```

## Связи

- Наследует: adr-unified-bot-router-v1 (entry-point, Ory lookup)
- Интегрирует: WP-384 (голосовой канал) — опциональный голосовой ответ если WP-384 Ф3 готова
- Параллельно: DP.SC.183 (LLM-диалог через бота)
- Использует: WP-324 Agent Inbox — если Day Open порождает задачи
