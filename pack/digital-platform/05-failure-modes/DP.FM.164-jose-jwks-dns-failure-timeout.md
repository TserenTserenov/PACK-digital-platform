---
id: DP.FM.164
name: "jose JWKS DNS-failure маскируется под request timed out"
type: failure-mode
domain: DP
status: active
valid_from: 2026-06-18
source: "session-transcript 2026-06-17, sessions/2026-06/2026-06-17-ory-incident-grafana-fix.md"
related:
  references: []
tags: [auth, jose, jwks, dns, timeout, jwt, ory]
---

# DP.FM.164 — jose JWKS DNS-failure маскируется под request timed out

## Паттерн

`jose` `createRemoteJWKSet` при DNS-сбое на JWKS-endpoint выдаёт **«request timed out»**, а не явный DNS error. Без DNS-healthcheck инцидент диагностируется как нестабильность сети, а не как DNS outage.

**Сигнал:** кластер JWT timeout-ошибок в узкий временной интервал (3+ за ≤20 минут).

## Последствие

JWT verification fails → пользователи получают 401. Диагностика задержана: команда ищет сетевую нестабильность, DNS-проблема определяется только через DNS healthcheck.

Система самовосстанавливается при DNS recovery (без вмешательства).

## Корневая причина

`jose` использует HTTP-fetch для получения JWKS от OIDC-провайдера. При DNS-lookup failure → fetch timeout → `jose` бросает `request timed out`, не DNS-specific error.

## Инцидент-пример

17.06.2026: DNS lookup failure на `mcp.aisystant.com` → 3 ошибки "request timed out" в user-profile-service за 20:02/20:11/20:18 UTC. DNS восстановился к утру 18.06.

## Диагностика

1. Кластер JWT timeout-ошибок за узкий интервал → подозрение.
2. `dig <oidc-host>` с узла сервиса: NXDOMAIN/timeout → DNS outage подтверждён.
3. Проверить DNS healthcheck в мониторинге.

## Профилактика

- DNS healthcheck для OIDC-провайдера → отдельный alert.
- Correlation rule: «N JWT timeout-ошибок за T минут» → автоматически добавить DNS-check к диагнозу.

## Связи

- Источник: session-transcript 2026-06-17, инцидент Ory DNS failure
