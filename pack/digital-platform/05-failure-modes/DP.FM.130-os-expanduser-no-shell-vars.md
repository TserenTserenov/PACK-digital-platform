---
id: DP.FM.130
title: os.path.expanduser не раскрывает shell-переменные ${VAR}
type: failure-mode
pack: DP
tags: [python, path-expansion, shell-variables, os-path, expanduser, expandvars]
status: active
valid_from: 2026-06-02
schema_version: 1
---

# DP.FM.130 — os.path.expanduser не раскрывает ${VAR}

## Симптом

`os.path.expanduser('~/IWE/${IWE_GOVERNANCE_REPO:-DS-strategy}/...')` возвращает путь с буквальным `${IWE_GOVERNANCE_REPO:-DS-strategy}` → `FileNotFoundError` или некорректный путь.

## Причина

`os.path.expanduser()` раскрывает **только** `~` → домашнюю директорию. Shell-переменные (`${VAR}`, `${VAR:-default}`, `$VAR`) остаются литеральными строками. Это задокументированное поведение, не баг.

## Fix

Разбить на два шага:

```python
# ВМЕСТО:
path = os.path.expanduser('~/IWE/${IWE_GOVERNANCE_REPO:-DS-strategy}/')

# ПРАВИЛЬНО:
repo = os.environ.get('IWE_GOVERNANCE_REPO', 'DS-strategy')
path = os.path.expanduser(f'~/IWE/{repo}/')
```

## Ловушки

- `os.path.expandvars()` раскрывает `$VAR`/`${VAR}`, но **НЕ** раскрывает bash-default синтаксис `${VAR:-default}` → неполное решение
- Комбинация: `os.path.expandvars(os.path.expanduser(path))` — работает для простых `$VAR`, но не для `${VAR:-default}`

## Профилактика

Не копировать bash-паттерны `${VAR:-default}` в Python строки. В Python всегда использовать `os.environ.get('VAR', 'default')`.

## Источник

git diff FMT-exocortex-template (103a14f, SKILL.md строка 630); WP-380 fixup-session 2026-06-02
