---
id: DP.FM.116
name: "Short-name fallback в authorization scope-check: cross-tenant bypass"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: security
severity: critical
valid_from: 2026-05-30
related:
  see_also: [DP.M.241]
tags: [security, authorization, multi-tenant, scope, oauth, cross-tenant, bypass]
source: "session 2026-05-30 WP-201 Ф3.4 mcp_tools (peer-session 30, review-01.md C1, report.md §4)"
schema_version: 1
---

# DP.FM.116 — Short-name fallback в authorization scope-check: cross-tenant bypass

## Описание

Authorization scope-checks для multi-tenant ресурсов с естественной иерархией (`owner/repo`, `tenant/project`, `org/team`) часто получают «ergonomic» расширение: «если пользователь указал только `repo` без `owner/`, fallback к short-name match». Эта relaxation создаёт **cross-tenant security bypass**: scope `acme/billing` ученика школы X совпадает по short-name с `globex/billing` ученика школы Y → ученик X получает доступ к данным Y.

Failure mode неочевиден: при правильном использовании (fully-qualified имена) код работает корректно. Bypass-path вызывается только short-name запросом — может месяцами лежать unused, потом обнаружиться при curious user / pentester.

## Симптом

- Pentester / curious user обнаруживает: запрос с short-name ресурса возвращает данные другого тенанта.
- В логах audit: цепочка `auth=allow` с записью «scope match via short-name fallback», тенант caller ≠ тенант ресурса.
- Тесты на правильно-сформированный input проходят зелёным; bypass-path не покрыт.

## Механизм

1. Author кода: «удобно, если пользователь может писать просто `billing` вместо `acme/billing`».
2. Реализация: `if claim.owner == req.owner and claim.repo == req.repo: allow; elif claim.repo == req.repo and req.owner is None: allow` (short-name fallback).
3. Пользователь acme/X получает access-token со scope `acme/billing`.
4. Пользователь acme/X делает запрос на ресурс с `req.owner=globex, req.repo=billing` — но через путь, где `req.owner` теряется (URL-route с одним segment, query без owner).
5. Validator видит `claim.repo == req.repo`, `req.owner is None` → allow → cross-tenant read.

## Canonical fix

Enforce **full match**: `claim.owner == req.owner AND claim.repo == req.repo`. Short-name НЕ принимать в auth-функции. На API-уровне:

- При short-name request — возвращать `400 Bad Request: scope must be qualified (owner/repo)`.
- Если short-name удобство критично — резолвить **до** authz через явный default-owner lookup (например, current_user.default_org). Default-owner — только из user context, никогда из request fallback.

## Тест применимости

«Функция авторизации принимает несколько эквивалентных форматов имени?» — да → red flag, проверить cross-tenant collision.

## Diagnostic

Audit-логи: scope claims в формате `<repo>` без owner — высокий приоритет review. Test suite: добавить cross-tenant test (юзер из X запрашивает ресурс Y по short-name).

## Применимо к

ACL, OAuth scopes, multi-tenant API, GitHub-like repo-scoped permissions, K8s RBAC namespace-fallback, любые scope-checks на иерархических ресурсах.