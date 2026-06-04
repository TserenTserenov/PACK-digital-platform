---
id: DP.ROLE.065
name: hermes-proxy-tool
title: "Прокси-инструмент Hermes-рантайма"
version: 1
status: draft
created: 2026-06-04
wp: WP-392
service_clause: DP.SC.167
---

# DP.ROLE.065 — Прокси-инструмент Hermes-рантайма

## Обязанности

Принять текстовый запрос от авторизованного клиента через MCP, проверить tier, прокси-запросить Hermes-рантайм, вернуть ответ или стандартизированную ошибку.

## Полномочия

- Читать `auth.userId` из JWT (без доверия к аргументам)
- Читать tier из env/subscription (не из аргументов)
- Вызывать HTTP endpoint Hermes-рантайма по `HERMES_RUNTIME_URL`
- Возвращать ошибку без объяснений при tier < T3

## Входы / Выходы (как артефакты)

| Тип | Артефакт |
|-----|----------|
| Вход | Текстовый запрос + Bearer токен |
| Выход | Ответ Hermes / ошибка tier / timeout-хинт |

## Связи

- Исполнитель: gateway-mcp (`src/index.ts`)
- Потребитель: бот (`clients/gateway_mcp.py`), Web UI, Claude.ai
- Зависит от: Hermes-рантайм (`hermes-agent` сервис Railway, `DP.SC.167`)
- Tier-источник: `auth.userId` → JWT `ext.tier` (Hydra token hook)

## Kind / Owner

- Kind: MCP-инструмент (stateless proxy)
- Owner: gateway-mcp сервис (CF Worker)
