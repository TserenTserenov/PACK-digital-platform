---
id: DP.FM.048
title: CF Bot Fight Mode блокирует Python XHR но не curl
type: fm
domain: digital-platform
tags: [cloudflare, http-client, bot-fight-mode, cli]
status: active
valid_from: 2026-05-18
schema_version: 1
---

# DP.FM.048 — CF Bot Fight Mode блокирует Python XHR но не curl

## Симптом

CLI-инструмент (Python `requests.post` / `httpx`) получает HTTP 403 с Challenge при обращении к CF Worker endpoint с включённым Bot Fight Mode.

## Причина

CF классифицирует автоматизированные Python HTTP-клиенты как бот-трафик по User-Agent + TLS fingerprint. `curl` не классифицируется как бот.

## Диагностика

HTTP 403 от CF Worker при автоматическом запросе, но тот же запрос через `curl -X POST <url> -d <payload>` возвращает 200.

## Fix

```python
subprocess.run(["curl", "-X", "POST", url, "-d", payload])
```
вместо `requests.post()`.

## Альтернатива

Отключить Bot Fight Mode для конкретного route через WAF rule в CF dashboard (требует доступа к CF dashboard).

## Применимо к

Любой headless Python-скрипт, записывающий данные на CF Worker endpoint с включённым Bot Fight Mode.

## Связи

- `lessons_cf_custom_domain_alias.md` — CF routing паттерны
- `lessons_cf_secret_gate_validation.md` — CF gate validation
