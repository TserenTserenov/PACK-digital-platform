---
id: DP.FM.227
name: "Bash set -e: [ cond ] && cmd внутри функции теряет exemption — функция возвращает exit 1"
type: fm
pack: PACK-digital-platform
domain: digital-platform / shell-scripting
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-close 2026-07-06, FMT-exocortex-template (commit 60e3591, fix #226)"
see_also: [DP.FM.033, DP.FM.192]
schema_version: 1
---

# DP.FM.227 — Bash set -e: обёртка [ cond ] && cmd в функцию меняет поведение

**Суть:** В основном теле скрипта `[ cond ] && cmd` exempt от `set -e` (bash трактует как conditional list). Тот же код внутри функции — уже нет: функция возвращает exit status последней команды, и её вызов в `set -e` контексте прерывает скрипт при exit 1.

## Механизм

```bash
# В основном теле — БЕЗОПАСНО
[ "$cond" ] && echo "msg"   # exit 1 при false не прерывает скрипт

# В функции — ОПАСНО
repair_pass() {
  [ "$cond" ] && echo "msg"  # возвращает 1 при false
}
repair_pass   # <- exit 1 от repair_pass() прерывает скрипт!
```

## Фикс

```bash
repair_pass() {
  [ "$cond" ] && echo "msg"
  return 0  # явный успешный выход
}
```

## Правило рефакторинга

При вытаскивании кода с `[ cond ] && cmd` в функцию → проверять финальный exit status функции; добавить `return 0` если функция «не может упасть».

## Связи

- DP.FM.033: bash arithmetic `((var++))` под set -e — другой класс той же темы
- DP.FM.192: subshell redirect глушит exit code — смежно
