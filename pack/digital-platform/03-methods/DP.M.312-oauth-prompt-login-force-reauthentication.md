---
id: DP.M.312
name: "OAuth prompt=login: принудительная re-authentication через стандартный параметр"
name_ru: "OAuth prompt=login: принудительная повторная аутентификация"
name_en: "OAuth prompt=login: force re-authentication via standard parameter"
summary: "Если клиент держит refresh-токен/grant и при reconnect не показывает форму входа, добавление параметра prompt=login (RFC 6749) к OAuth-authorize URL заставляет identity-провайдер игнорировать существующую сессию и потребовать свежую аутентификацию."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: confirmed
epistemic_stage: 2
category: identity-session-management
valid_from: 2026-06-14
related:
  see_also: []
tags: [oauth, kratos, ory, prompt-login, identity, reauthentication, mcp]
source: "WP-411 Ф5, session-transcript 2026-06-14, commit eb9599c0d"
schema_version: 1
---

# DP.M.312 — OAuth prompt=login: принудительная re-authentication

## Описание

Когда OAuth-клиент (например, MCP-коннектор claude.ai) держит refresh-токен/grant, переподключение через disconnect/reconnect молча переиспользует существующий grant — форма входа не появляется (см. различение «Переподключение клиента ≠ отзыв OAuth-grant»). Добавление параметра `prompt=login` к authorize-URL provider'а заставляет provider игнорировать существующую сессию и потребовать свежую аутентификацию.

## Алгоритм (IPO)

**Вход:** клиент-OAuth держит активный grant, пилот хочет сменить аккаунт
**Процесс:**
1. К URL authorize-эндпоинта identity-провайдера добавить параметр `prompt=login` (например, `https://provider/oauth/authorize?...&prompt=login`)
2. Provider игнорирует существующую сессию
3. Provider показывает форму входа независимо от наличия активной сессии
**Выход:** свежая аутентификация под новым аккаунтом, новый grant

## Применимость

| Сценарий | Триггер | Эффект |
|----------|---------|--------|
| Смена аккаунта в подключённом MCP | Пилот хочет переключиться с email A на email B | Connector показывает форму входа вместо silent reuse |
| Тест смены email | QA-сценарий | Воспроизводимый flow без чистки cookie |
| Enterprise onboarding | Единый аккаунт-шаблон для команды | Гарантия чистого входа каждого участника |

## Стандарт

`prompt` parameter — часть OAuth 2.0 (RFC 6749 / OpenID Connect Core). Значения:
- `none` — silent reuse (по умолчанию)
- `login` — force re-authentication
- `consent` — re-prompt consent screen
- `select_account` — show account selector

В IWE-кейсе (WP-411 Ф5) применён `prompt=login` для Kratos.

## Failure modes (когда НЕ работает)

- **Провайдер не поддерживает RFC 6749 prompt:** редко, но возможно у custom identity-провайдеров
- **Browser-flow без интервенции клиента:** если клиент не управляет authorize-URL (например, deep-link), параметр не добавится
- **Кэш на стороне браузера/MCP-клиента:** некоторые клиенты кэшируют authorize-URL без `prompt`

## Тест применимости

«Видит ли connector окно входа после reconnect?» Нет → grant жив; добавить `prompt=login`. Да → защита работает.

## Связи

- Различение «Переподключение клиента ≠ отзыв OAuth-grant» (WP-411 Ф5, 2026-06-11) — обосновывает почему disconnect/reconnect недостаточен
- Различение «Kratos browser flow ≠ Kratos API flow» — параметр работает в обоих, прикладывается к authorize-URL
