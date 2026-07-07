---
id: DP.FM.192
title: "`>/dev/null 2>&1` после subshell-блока поглощает exit code: тихий PASS при падении команды"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / shell-scripting
epistemic_stage: confirmed
valid_from: 2026-07-05
source: "session-close 2026-07-03 (DS-my-strategy f5c672138, fix(hooks): active-wp-rebuild-guard)"
related:
  see_also: [DP.FM.038, DP.FM.085]
---

# DP.FM.192 — `>/dev/null 2>&1` после subshell-блока поглощает exit code: тихий PASS при падении команды

## Описание

`(cd "$REPO" && some_command) >/dev/null 2>&1` — перенаправление стоит **после** subshell-блока `()`. Exit code subshell поглощается redirect'ом. Последующая проверка `$?` или `git diff --quiet` работает против состояния **до** упавшей команды → false-green.

## Пример

```bash
# СЛОМАНО:
(cd "$REPO" && python3 build-active-wp.py) >/dev/null 2>&1
git diff --quiet active-wp.md || echo "changed"  # сравнивает нерегенерированный файл!

# ПРАВИЛЬНО:
if ! (cd "$REPO" && python3 build-active-wp.py) >/dev/null 2>&1; then
  echo "build failed" >&2; exit 1
fi
git diff --quiet active-wp.md || echo "changed"
```

## Симптом

- Guard всегда зелёный
- Артефакт не обновляется (падение команды молчит)
- Нет явной ошибки → поведение неправильное, но необнаружимое

## Тест обнаружения

«В pipeline с `>/dev/null 2>&1` — захвачен ли exit code явно через `if ! (...)` или переменную `$?` ДО redirect?» Нет → уязвимость к false-green.

## Инвариант

Любой pipeline с `>/dev/null 2>&1` и последующей проверкой инвариантов должен явно захватывать exit code через `if ! (...)` до redirect.

## Связи

- DP.FM.038 (Validator silent pass) — более широкий паттерн «тихого прохождения проверки»
- DP.FM.085 (Hook-installer anti-patterns) — связанный контекст shell scripting в hooks
