---
id: DP.METHOD.132
name: GitHub App over fine-grained PAT for team pipelines
name_ru: GitHub App вместо fine-grained PAT для командных конвейеров
type: method
status: established
summary: "При командной разработке (>1 человека имеют доступ к репо) → GitHub App обязателен для постоянных конвейеров. Fine-grained PAT привязан к личному аккаунту — команда не видит, не может ротировать; срок ≤1 год = дополнительный административный долг. GitHub App — org-scoped: видим и ротируется централизованно."
created: 2026-07-09
trust:
  F: 2
  G: domain
  R: 0.90
epistemic_stage: established
related:
  complements: [DP.METHOD.133]
tags: [github, automation, security, pat, github-app, team, credentials, pipeline]
wp: WP-415
sources:
  - session-transcript 2026-07-06
  - git diff DS-my-strategy commit deafe21ed
---

# DP.METHOD.132 — GitHub App вместо fine-grained PAT для командных конвейеров

## Контекст

При создании постоянного GitHub-конвейера (кросс-репозиторные операции, автопубликация) нужно выбрать actor'а — владельца credentials для GitHub API.

## Правило выбора

**Если командная разработка (>1 человека с доступом к репо):**
→ **GitHub App** обязателен для постоянных конвейеров.

**Если одиночный аккаунт, личный одноразовый скрипт:**
→ fine-grained PAT допустим.

## Сравнение

| Критерий | GitHub App | Fine-grained PAT |
|----------|-----------|-----------------|
| Видимость для команды | org-scoped, видим всем | привязан к личному аккаунту |
| Ротация | централизованная через org | только владелец аккаунта |
| Срок жизни | без принудительного истечения | ≤1 год, требует renewal |
| Административный долг | минимальный | отдельный пункт в runbook |

## Инвариант

Fine-grained PAT с командным доступом = single point of failure: если аккаунт владельца недоступен или токен истёк → конвейер остановлен, команда не может исправить самостоятельно.
