---
id: DP.SC.167
name: hermes-chat
title: "MCP-инструмент hermes_chat — прокси к Hermes-рантайму"
version: 1
status: draft
created: 2026-06-04
wp: WP-392
roles: [DP.ROLE.065]
---

# DP.SC.167 — hermes_chat

## Обещание

Инструмент `hermes_chat` в gateway-mcp принимает текстовое сообщение пользователя, проксирует его к Hermes-рантайму и возвращает ответ. Пользователь получает ответ от Hermes без прямого доступа к рантайму; gateway выступает единственной точкой входа.

## Триггер

Вызов MCP-инструмента `hermes_chat` от авторизованного клиента (бот, Web UI, Claude.ai) с валидным Bearer-токеном и tier ≥ T3.

## Входы

| Поле | Тип | Обязательно | Описание |
|------|-----|-------------|----------|
| `message` | string | да | Текст запроса пользователя |
| `session_id` | string | нет | ID диалоговой сессии (для памяти Hermes) |

Запрещено: поле `user_id` в аргументах — берётся из `auth.userId` проверенного JWT-токена.

## Выходы

**Успех (sync):**
```json
{ "response": "...", "model": "anthropic/claude-opus-4-5", "latency_ms": 3200 }
```

**Timeout (>25s):**
```json
{ "error": "timeout", "message": "Запрос обрабатывался слишком долго (лимит 25 секунд). Попробуй упростить вопрос или разбить на части." }
```

**Tier недостаточен:**
```json
{ "error": "tier_required", "required": 3, "current": 1 }
```

## Время отклика

- SLA p95: ≤ 25s (CF Worker hard limit 30s, запас 5s)
- Ack при timeout: немедленно (бот показывает сообщение-хинт)

## Инварианты

1. `user_id` для Hermes ВСЕГДА берётся из JWT `auth.userId`, не из аргументов инструмента
2. Tier-проверка происходит в gateway ДО вызова Hermes-рантайма
3. `HERMES_RUNTIME_URL` и `HERMES_RUNTIME_TIMEOUT` — обязательные env vars

## MVP-ограничения

- Tier enforcement работает только при наличии `contract.tier` в subscription DB.
  До деплоя миграции `ADD COLUMN contract.tier` — T3/T4 acceptance tests требуют временного мока (вариант A).
  Вариант B (настоящая миграция) — отдельный РП, решение по срокам у пилота.
- Async/polling (`job_id`) — не в MVP. Точка расширения: при >10% timeout-ов за 7 дней рассмотреть Ф3.2 с async.

## Режим отказа

| Ситуация | Поведение |
|----------|-----------|
| Hermes недоступен (HTTP 5xx) | `{ "error": "hermes_unavailable" }` + лог в Railway |
| Hermes timeout (>25s) | Сообщение-хинт пользователю |
| Неверный/просроченный токен | HTTP 401 от gateway (до вызова Hermes) |
| Tier < T3 | `{ "error": "tier_required" }` — бот показывает «Функция недоступна на твоём тире» |

## Сценарии использования (≥3)

**SC-1: Бот-чат (основной)**
Пользователь T3 пишет в @aist_me_bot. Бот → gateway-mcp `hermes_chat` → Hermes Railway → ответ → бот показывает пользователю. Latency: 3-8s для простых вопросов.

**SC-2: Длинный запрос (timeout)**
Пользователь T3 задаёт сложный вопрос с tool use. Gateway держит 25s, возвращает timeout. Бот показывает «слишком долго, упрости вопрос». Пользователь переформулирует.

**SC-3: Tier T2 (блокировка)**
Пользователь T2 пишет свободный текст. Бот проверяет tier из `_tokens` кеша, НЕ вызывает `hermes_chat`, показывает «Функция недоступна на твоём тире».

## Ссылки

- Роль: [DP.ROLE.065](../02-domain-entities/DP.ROLE.065-hermes-proxy-tool.md)
- Деплой Hermes: `iwe-server-config/hermes-agent/DEPLOY.md`
- РП: WP-392 Ф3.1
