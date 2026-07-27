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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Автоматическая гарантия актуальности derived-файла (hook при каждом Edit/Write source) ↔ non-blocking поведение (exit 0 при fail, см. Свойства) | Хук обещает свежесть derived-файла, но по требованию не блокирует работу при ошибке регенерации — значит, само обещание держится только пока кто-то отдельно проверяет `--check` |
| `--check` режим как жёсткий CI-gate (exit 1 при drift) ↔ повседневный non-blocking режим (exit 0 при fail) | Один и тот же механизм hook сконфигурирован по-разному для CI и для обычной работы — несовпадение режимов легко перепутать при отладке |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `draft`: пометка `tentative` по прецеденту SA.METHOD.001 (WP-448 Ф12)._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Восприятие non-blocking как «безопасно, значит проверяется» | Внимание смещается на удобство «hook не прерывает работу при ошибке» (Свойства, non-blocking), недооценивая, что exit 0 при fail означает: без отдельного запуска `--check` в CI рассинхронизация source→derived может накапливаться незамеченной сколь угодно долго |
| _(tentative)_ Матчер `Edit\|Write` воспринимается как специфичный для source-файла | При переносе паттерна на новую пару source→derived (см. «Паттерн source → derived») внимание съезжает на переиспользование блока «Конфигурация» как есть, не проверяя, что matcher `Edit\|Write` в PostToolUse срабатывает на любой Edit/Write в репо — фильтрация по факту происходит только внутри скрипта (`--source` флаг), не в самой hook-конфигурации |

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

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 2). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
