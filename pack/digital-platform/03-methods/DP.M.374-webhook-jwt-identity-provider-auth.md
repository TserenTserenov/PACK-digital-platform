---
id: DP.M.374
type: method
title: webhook-jwt-identity-provider-auth — верификация webhook через JWT identity provider вместо shared-secret
kind: Method
pack: PACK-digital-platform
domain: digital-platform / security-patterns
trust: observed
epistemic_stage: confirmed
domains: [webhook, security, oauth2, jwt, identity-provider]
source_session: 2026-07-07 session-close (WP-467 fix)
valid_from: 2026-07-07
schema_version: 1
related:
  see_also: [DP.M.021, DP.M.329]
---

# DP.M.374 — Webhook через JWT identity provider вместо shared-secret

## Определение

При использовании identity provider (Ory, Auth0, Keycloak) для платформы — webhook endpoint биллинга/событий можно верифицировать через JWT/OAuth2 bearer token вместо статического shared-secret.

## IPO

- **Вход:** входящий webhook запрос с Authorization header (bearer token) или без shared-secret
- **Процесс:** верифицировать JWT через jwks_uri identity provider; проверить claims (sub, iss, scope)
- **Выход:** подтверждённая принадлежность запроса к авторизованному отправителю

## Когда применять

- Платформа уже использует identity provider (Ory Hydra/Kratos, Auth0 и др.)
- Webhook endpoint принадлежит внутренней экосистеме — нет необходимости в стороннем shared-secret
- Требуется централизованное управление доступом (ротация ключей через IAM, а не ручная)

## Преимущества перед shared-secret

| Shared-secret | JWT через IDP |
|---------------|---------------|
| Статический секрет на стороне получателя | Нет дополнительного секрета (verif через JWKS) |
| Ручная ротация при компрометации | Ротация через IAM (централизованная) |
| Нет granular scopes | Claims: scope, sub, iss — fine-grained |
| Утечка = полная компрометация endpoint | Утечка токена = ротировать через IDP |

## Ограничение и проверка

Перед внедрением убедиться: **поддерживает ли billing/event provider bearer token вместо webhook secret** для конкретного endpoint. Stripe, например, поддерживает только HMAC-подпись для стандартных webhook events — JWT через IDP применим к custom/internal webhook.

## Связано

- DP.M.021 — GitHub App platform integration (другой паттерн аутентификации через app token)
- DP.M.329 — webhook idempotency db-constraint (дополняющий: сначала верифицируй, потом idempotency)
- **Связи:** DP.M.266 (shared-secret + X-User-ID для internal services — другой контекст: inbound webhook vs service-to-service)
