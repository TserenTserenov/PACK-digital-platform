---
id: DP.FM.083
type: failure-mode
title: "Empty-field URL injection через пустой github_owner"
pack: PACK-digital-platform
domain: platform-operations
status: active
valid_from: 2026-05-25
source: "session-close 2026-05-25 (bd8395d, 4e22993 DS-autonomous-agents)"
schema_version: 1
---

# DP.FM.083: Empty-field URL injection через пустой github_owner

## Описание

managed-пилоты (не GitHub-connected) имеют `github_owner=""`. Любое место, строящее URL через конкатенацию `github_owner + "/" + repo`, генерирует битую ссылку вида `https://github.com//personal-guide`.

## Класс дефекта

Empty-field URL injection — конкатенация строковых полей без проверки пустого значения.

## Симптом

Admin TG-уведомления, web UI, email-шаблоны показывают невалидные URL с двойным слэшем.

## Паттерн фикса

```python
if not github_owner:
    display = "(managed)"
else:
    display = f"https://github.com/{github_owner}/{repo}"
```

Проверка перед **любой** конкатенацией с `github_owner`.

## Потенциальные места появления

- Admin summary
- Web UI profile
- TG-уведомления (admin + user-facing)
- Email-шаблоны
- Любое место, строящее personal-guide URL

## Источник

Commits bd8395d, 4e22993 в DS-autonomous-agents. Session-close 2026-05-25.
