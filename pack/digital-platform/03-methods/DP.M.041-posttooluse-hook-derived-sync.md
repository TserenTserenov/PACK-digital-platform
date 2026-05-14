---
id: DP.M.041
title: "PostToolUse Hook для синхронизации производных файлов"
type: method
pack: PACK-digital-platform
domain: digital-platform
tags: [hooks, derived-files, automation, source-of-truth, sync, settings.json]
valid_from: 2026-05-13
status: draft
---

# DP.M.041 — PostToolUse Hook для синхронизации производных файлов

## Определение

Паттерн автоматической синхронизации производного файла с source-of-truth через PostToolUse hook при каждом изменении источника.

## Описание

**Паттерн:** source-of-truth файл (S) → производный файл (D) синхронизируется автоматически через PostToolUse hook в `.claude/settings.json`.

**Пример:** Edit/Write `WP-REGISTRY.md` → автоматическая перегенерация `current/active-wp.md`.

## Конфигурация

```json
// .claude/settings.json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "command": "scripts/build-active-wp.py --source WP-REGISTRY.md --output current/active-wp.md"
      }
    ]
  }
}
```

## Свойства

| Свойство | Описание |
|---------|---------|
| **Non-blocking** | Hook не прерывает работу при ошибке регенерации (exit 0 при fail) |
| **`--check` режим** | exit 1 при drift без перезаписи — пригоден как CI-gate |
| **Idempotent** | Повторный запуск не ломает результат |

## Паттерн «source → derived»

Применяется для любых пар:
- `REGISTRY.md` → index / active-wp.md
- `.proto` → generated code
- `schema.json` → docs/api.md
- `CLAUDE.md` → rules-registry.yaml

## Тест применимости

Пользователь видит актуальный derived-файл **автоматически** без явного вызова скрипта.

## Отличие от DP.SC.025 (capture-bus)

DP.SC.025: PostToolUse → запуск детекторов поведения агента.
DP.M.041: PostToolUse → синхронизация specific source→derived пары.
Оба используют один механизм hook, но для разных целей.

## Источник

WP-5 Ф-15 (2026-05-13). Реализация: `DS-my-strategy/scripts/build-active-wp.py`, commit 4e58952f.
