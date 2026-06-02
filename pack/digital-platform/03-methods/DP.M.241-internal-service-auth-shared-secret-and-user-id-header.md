---
id: DP.M.241
name: "Internal service auth: shared secret + X-User-ID header вместо user_jwt propagation"
type: method
status: active
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
valid_from: 2026-05-30
related:
  see_also: [DP.FM.020]
tags: [auth, microservices, least-privilege, service-to-service, jwt, accounting]
source: "session 2026-05-30 WP-201 Ф3.4 mcp_tools (peer-session 30, 01-peer.md:17-21, report.md §2 Тема 2)"
schema_version: 1
---

# DP.M.241 — Internal service auth: shared secret + X-User-ID header вместо user_jwt propagation

## Суть метода

При построении внутреннего service-to-service вызова (agent-runner → LLM-proxy, worker → cache-tier) возникает соблазн прокидывать raw user-токен (user_jwt, session_id) для per-user accounting и rate-limit на стороне зависимого сервиса. Method предлагает альтернативу с меньшим blast radius: **shared service-level secret** (`PROXY_SHARED_SECRET` в env, required field в Settings) + **`X-User-ID` header** для accounting. Backend-сервис доверяет user-ID claim'у благодаря shared-secret и не валидирует JWT повторно.

## Когда применять

- Зависимый сервис — внутри trusted boundary (один tenant, один deployment unit, один control plane).
- Зависимому сервису нужен только user-ID для accounting/rate-limit, не полный scope-set.
- Хочется минимизировать blast radius при компрометации внутреннего сервиса.

## Реализация

1. Сгенерировать high-entropy secret (`openssl rand -hex 32`), записать как required field в Settings обоих сервисов (caller и callee).
2. Caller в каждом запросе добавляет два header'а:
   - `Authorization: Bearer ${PROXY_SHARED_SECRET}` — proof of internal origin.
   - `X-User-ID: <uuid>` — для accounting/rate-limit.
3. Callee middleware:
   - Constant-time сравнение `Authorization` с собственным `PROXY_SHARED_SECRET` → 401 при mismatch.
   - При passed check — читает `X-User-ID`, использует для logging/metrics/rate-limit.
4. Token rotation: каждые N дней / по incident — оба сервиса обновляются одновременно через deployment с blue-green.

## Антипаттерн (что НЕ делать)

Передача raw user_jwt между внутренними сервисами:

- **Blast radius:** компрометация internal-сервиса утекает пользовательские токены, не только service-level secret.
- **Token rotation / revocation:** усложняются — теперь N сервисов видят токен, каждый может его кэшировать.
- **Audit:** размывается — «кто это сделал, пользователь или сервис от его имени?»
- **Scope creep:** внутренний сервис получает full user scope, хотя ему нужен только ID.

## Tradeoff

Теряется возможность зависимому сервису проверить scope-claims пользователя самостоятельно. Если ему это критично — нужен отдельный механизм (например, JWT exchange + свежий public key), не raw propagation.

## Тест применимости

«Зависимому сервису нужно знать полный set scope'ов пользователя?»

- Нет → shared secret + user-ID header (этот method).
- Да → SAT/JWT validation на стороне callee с актуальной PKI.

## Не применять для

Public-facing API gateway (там user должен явно подтвердить scope через signed JWT). Cross-tenant boundary (нет общего trust boundary).