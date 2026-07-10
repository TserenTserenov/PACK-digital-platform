---
id: DP.FM.228
name: "Railway liveness probe падает на 401 при /health за auth"
type: fm
pack: PACK-digital-platform
domain: digital-platform / deployment
trust: confirmed
epistemic_stage: observed
valid_from: 2026-06-09
source: "git diff DS-my-strategy (inbox/WP-406/WP-406.md + commit 39cfe0716 /liveliness fix)"
schema_version: 1
---

# DP.FM.228 — Railway liveness probe падает на 401 при /health за auth

## Симптом

Сервис запустился и работает, но Railway помечает его как unhealthy — деплой зависает или сервис циклически рестартует.

## Причина

Railway использует `/health` или `/liveliness` эндпоинт для liveness probe в `railway.toml`. Если этот эндпоинт защищён auth middleware (`GATEWAY_SHARED_SECRET`), Railway получает 401 на probe-запрос и интерпретирует это как «сервис упал».

## Fix

Liveness-эндпоинт для Railway обязан быть без auth (`/liveliness`, `/ping`) — отдельный от бизнес-эндпоинтов `/health`, защищённых shared-secret.

## Диагностика

Сервис логирует успешный запуск, но Railway помечает как unhealthy → проверить, требует ли auth-middleware секрет на probe-эндпоинте.

## Применимо к

Всем сервисам с shared-secret middleware и Railway deployment.

## Источник

git diff DS-my-strategy (inbox/WP-406/WP-406.md, commit 39cfe0716, railway.toml healthcheck /liveliness)
