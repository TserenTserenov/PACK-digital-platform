---
id: DP.SC.183
title: "LLM-диалог IWE через бота"
status: draft
version: "0.1"
created: 2026-06-19
wp: WP-428
depends_on:
  - adr-unified-bot-router-v1
tier_required: T1+
privacy_class: privacy-by-design
---

# DP.SC.183 «LLM-диалог IWE через бота»

## Обещание

Пользователь IWE может задать вопрос к своему руководству, системе или коучу через Telegram-бота и получить персонализированный ответ в разговорном формате.

## Триггеры

- Входящий текст с `.`-prefix (например: «. что мне делать с системным мышлением?»)
- Команда `/iwe <текст>`

## Входы

- `telegram_id` — идентифицирует пилота через UnifiedBotRouter (adr-unified-bot-router-v1)
- `text` — текст запроса пользователя

## Тир-разветвление

| Тир | Контекст | Модель |
|-----|----------|--------|
| T3+ | `get_user_context` + `dt_read_digital_twin` загружаются как system-prompt | hermes_chat (Aisystant MCP) |
| T1-T2 | Без персонального контекста | Haiku (fallback) |

## Privacy by design (ИНВАРИАНТ)

Персональный контекст (cp-профиль, активные РП, цели, history) используется ТОЛЬКО как system-prompt для LLM. В Telegram-ответ идёт только синтезированный текст — без raw атрибутов.

**Non-regression smoke invariant:** ответ не содержит структурных PII-паттернов:
- `WP-\d+` (номера задач)
- `cp\.\w+=` (значения профиля)
- `@\w+\.` (email-адреса)

Нарушение любого паттерна в ответе = regression, требует immediate fix.

## SLA

| Тип | Критерий | SLA |
|-----|----------|-----|
| light | ≤200 tokens в ответе | P95 ≤45с (sync) |
| heavy | >200 tokens | async: typing action + нотификация каждые 15с |

Роутинг light/heavy: по DP.SC.162.

## Consent

T3 grant (подключение через любой AI-клиент: claude.ai OAuth, /connect_external, Claude Code) является необходимым и достаточным consent'ом при соблюдении privacy by design. Отдельный notice не требуется, т.к. raw PII не покидает Aisystant MCP boundary.

## Fallback

| Ситуация | Поведение |
|----------|-----------|
| LLM недоступен | Error envelope из adr-unified-bot-router-v1 |
| Ory lookup fail | Onboarding CTA |
| Timeout (>45с light) | «Ответ занял больше времени. Попробуй в VS Code.» |

## Свидетельства результата

- `bot.llm_dialog.response` domain event (без тела, только tier + latency_ms)
- HTTP 200 от Telegram sendMessage API

## Связи

- Наследует: adr-unified-bot-router-v1 (entry-point, Ory lookup)
- Переиспользует: DP.SC.162 (light/heavy routing)
- Заменяет: DP.SC.169 (Гермес-prefix) — unified handler поглощает
- Параллельно: DP.SC.184 (Day Open через бота)
