---
id: DP.FM.116
title: "Path traversal через внешний идентификатор"
type: failure-mode
status: draft
domain: digital-platform
valid_from: 2026-05-27
source: WP-358, fix session_id path-traversal guard
---

# DP.FM.116 — Path Traversal через внешний идентификатор

## Симптом

Агент принимает идентификатор из внешнего источника (Telegram, webhook, GitHub trigger) и использует его для построения пути к локальному файлу без валидации. Злоумышленник передаёт `../../../etc/passwd` вместо `SESSION-abc123`.

## Причина

Отсутствие whitelist-валидации внешнего идентификатора до файловых операций.

## Fix

Validate before use:
```python
import re
if not re.match(r'^SESSION-[A-Za-z0-9-]+$', session_id):
    raise ValueError("Invalid session_id")
```

## Паттерн

**Правило:** любой внешний идентификатор, используемый для построения файлового пути, обязан проходить whitelist-regex до операции.

## Применимость

Любой агент с git-based state store, принимающий пользовательский идентификатор.

## Связи

- SC: DP.SC.162 (External Session Request)
