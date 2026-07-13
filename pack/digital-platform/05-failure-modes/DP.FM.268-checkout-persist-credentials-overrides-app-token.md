---
id: DP.FM.268
type: failure-mode
title: checkout-persist-credentials-overrides-app-token — actions/checkout с persist-credentials:true перекрывает embedded app token
trust: observed
epistemic_stage: confirmed
domains: [ci-cd, github-actions, multi-repo, credentials, authentication]
source_session: 2026-07-07 session-close (git diff FMT-exocortex-template, translate-sync.yml)
valid_from: 2026-07-07
schema_version: 1
related:
  see_also: [DP.FM.020, DP.FM.023]
---

# DP.FM.268 — actions/checkout с persist-credentials:true перекрывает embedded app token

## Симптом

Multi-repo workflow: `actions/checkout` + последующий push в другой репозиторий через `https://x-access-token:<app-token>@github.com/org/repo` URL. Push возвращает 403. Credentials-log показывает `github-actions[bot]` вместо app identity.

## Корень

`actions/checkout@v3+` по умолчанию имеет `persist-credentials: true`. Он записывает GITHUB_TOKEN в git credential store по хосту `https://github.com/`. Credential store отдаёт **первое совпадение по хосту**, независимо от репозитория. Embedded app token в remote URL имеет тот же хост — credential store его игнорирует и отдаёт GITHUB_TOKEN.

```
# Что происходит:
git config credential.helper  # → store (set by actions/checkout)
# Запись: username=x-access-token, password=<GITHUB_TOKEN> for https://github.com/
# Embedded URL: https://x-access-token:<APP_TOKEN>@github.com/org/other-repo
# Credential store: видит github.com → отдаёт GITHUB_TOKEN → 403 для other-repo
```

## Профилактика

**Правило:** при multi-repo workflow с разными токенами на одном хосте (github.com) — ВСЕГДА `persist-credentials: false` на шаге checkout:

```yaml
- uses: actions/checkout@v4
  with:
    persist-credentials: false
```

**Тест:** «Workflow делает push в репозиторий, отличный от checkout'ed?» → проверить `persist-credentials: false`.

## Обнаружение

Симптом: 403 при push, несмотря на явный токен в URL. `GIT_TRACE_CURL=1` показывает GITHUB_TOKEN в Authorization header вместо APP_TOKEN.

## Применимо к

- Любой GitHub Actions workflow с `actions/checkout` + push в другой репо с другим токеном
- Особенно: canonical source → mirror publish workflows, org-to-org cross-repo operations
- IWE-специфично: FMT-exocortex-template → iwesys/iwe-template canon-sync

## Связано

- DP.FM.020 — gateway-upstream-credentials-undisclosed (смежный: hidden credentials mismatch)
- DP.FM.023 — service-user-credentials-path (смежный: wrong credential scope)
- DP.FM.267 — publish-job-in-mirror-not-source (то же семейство инцидентов canon-sync)
- DP.SC.188 — canon-relay-iwe-template (то же семейство инцидентов canon-sync)
