---
id: DP.METHOD.051
type: method
name: n8n встроенный /healthz endpoint для внешнего мониторинга
description: Использовать нативный GET /healthz endpoint n8n вместо отдельного воркфлоу для health-check мониторинга
domain: digital-platform
pack: PACK-digital-platform
valid_from: 2026-05-29
---

# DP.METHOD.051 — n8n встроенный /healthz endpoint

## Описание

n8n предоставляет нативный `GET /healthz` endpoint (HTTP 200, body `{"status":"ok"}`), доступный без аутентификации, пока процесс жив. Отдельный воркфлоу для health-check не нужен.

## Когда применять

При деплое n8n на Railway или других хостингах, когда нужен внешний мониторинг (BetterStack, UptimeRobot и т.п.).

## Алгоритм

1. Настроить монитор на `GET https://<n8n-host>/healthz`
2. Ожидаемый ответ: HTTP 200, body содержит `"status":"ok"`
3. Keyword-check обязателен (HTTP 200 + 0 bytes = false-green без него)

## Ограничения

- Проверяет только «жив ли процесс», не «работает ли LLM pipeline»
- Для полной проверки пайплайна нужен отдельный probe (DP.FM.105)

## Связи

- Решает: `05-failure-modes/DP.FM.105-internal-probe-blind-to-own-failure.md`
- Дополняет: `lessons/lessons_n8n_railway_ops.md` (CORS + webhook registry паттерны)