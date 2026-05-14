---
id: DP.M.026
type: method
name: git-fork-push-pattern
title: "Git fork-flow: проверка upstream перед push в org-репо"
domain: digital-platform
pack: PACK-digital-platform
valid_from: 2026-05-12
status: active
schema_version: 1
---

# DP.M.026 — Git Fork-Flow Push Pattern

## Контекст

Разработчик работает в личном форке org-репозитория (`origin` = личный форк, `upstream` = org-репо). При push без проверки коммит уходит в личный форк, а не в org.

## Метод

**Перед каждым push в организационный репозиторий:**

```bash
git remote -v
# Если есть remote 'upstream' → репо является форком org
# push в upstream (org) + синхронизировать origin (личный форк)
git push upstream HEAD
git push origin HEAD  # опционально: синхронизировать форк
```

**Критерий наличия upstream:**
- `git remote -v` показывает `upstream` → org-репо
- Только `origin` → независимый репо, push как обычно

## Алгоритм (IPO)

**Вход:** намерение сделать push
**Процесс:**
1. `git remote -v` → проверить наличие `upstream`
2. Если `upstream` есть → `git push upstream HEAD`
3. Синхронизировать `origin` при необходимости

**Выход:** коммит в org-репо + форк синхронизирован

## Применимость

Любые проекты с fork-flow: платформенные SDK, org-репо с контрибьюторами, совместные Pack-репозитории.

## Связи

- Память (feedback): `memory/feedback_push_upstream_for_org_repos.md`
- Failure mode: коммит попал в личный форк вместо org (инцидент WP-73, 12 мая 2026)
