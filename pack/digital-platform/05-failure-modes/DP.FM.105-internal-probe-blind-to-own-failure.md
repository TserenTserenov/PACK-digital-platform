---
id: DP.FM.105
name: Внутренний health-probe слеп к собственным падениям
type: failure-mode
domain: digital-platform
pack_refs: []
status: active
valid_from: 2026-05-28
schema_version: 1
---

# DP.FM.105 — Внутренний health-probe слеп к собственным падениям

## Описание

Внутренний health-probe (запускаемый внутри той же системы, которую он мониторит) не способен обнаружить падение собственного хоста или платформы. При полном отказе сервиса (все deployments REMOVED, OOM-kill, сетевая изоляция) внутренний probe тоже перестаёт работать.

## Симптом

Сервис упал. Внутренний мониторинг молчит — не потому что всё хорошо, а потому что мониторинг упал вместе с сервисом. Инцидент обнаруживается по жалобам пользователей, не по алерту.

## Тест

«Может ли мониторинг упасть вместе с сервисом?» Да → внутренний probe, не внешний.

## Правило

Внешний keyword-монитор (POST/GET + ожидаемый ключ в response body) обязателен с первого дня деплоя любого webhook/API сервиса, не «потом добавим».

Внешний монитор:
1. Запускается с другого хоста (BetterStack, UptimeRobot, Grafana Cloud)
2. Делает реальный HTTP-запрос к публичному URL
3. Проверяет ключевое слово в теле ответа (keyword-check), а не только HTTP 200
4. Алертит при отсутствии ожидаемого тела или таймауте

## Примеры

- n8n на Railway: `/webhook/check` с keyword `{"status":"ok"}` → BetterStack monitor
- FastAPI worker: `/health` с keyword `"ready"` → UptimeRobot
- CF Worker: `GET /ping` с keyword `pong` → внешний cron-probe

## Антипаттерн

Внутренний n8n workflow `mcp-health-probe` → не ловит падение самого n8n. HTTP 200 + 0 bytes = false-green (cf. lessons `HTTP status check ≠ keyword check`).
