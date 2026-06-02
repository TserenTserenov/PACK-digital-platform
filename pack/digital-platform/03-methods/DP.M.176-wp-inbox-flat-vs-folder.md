---
id: DP.M.176
name: "WP Inbox: flat-file vs folder structuring"
type: method
pack: PACK-digital-platform
domain: digital-platform
trust: validated
epistemic_stage: confirmed
valid_from: 2026-05-25
---

# DP.M.176 — WP Inbox: Flat vs Folder (правило структурирования)

## Проблема

Двойная структура (плоские файлы + папки для одного РП) порождает navigation debt и ломает скрипты sweep/status.

## Правило

| Условие | Структура |
|---------|-----------|
| РП < 5 файлов | Плоский файл `WP-NNN-slug.md` в корне inbox |
| РП ≥ 5 файлов или наличие поддокументов | Папка `WP-NNN/` с обязательным `index.md` |

`index.md` должен содержать: frontmatter (id, status, budget) + ссылки на все вложенные файлы.

## Дополнительные правила

- Артефакты, принадлежащие РП, хранятся рядом с РП (в папке), не в корне inbox
- Скрипты sweep читают `index.md` как точку входа; flat-файлы — напрямую
- `bottleneck-pick --target WP-NNN` → пишет в `WP-NNN/bottleneck-picks/YYYY-MM-DD.yaml`
- Критерий перехода к папке — **прогнозируемый** размер при создании, не ретроактивный

## Применение

При создании нового РП — выбрать структуру исходя из прогнозируемого числа файлов.
При миграции legacy: унифицировать за один коммит (`git mv` + commit).

Stage A check: зонтичные РП (WP-250, WP-290, WP-330, WP-337, WP-353) → проверить на status=done/deferred перед нормализацией.

## Связи

- Rule anchor: DS-my-strategy/CLAUDE.md §«Правило inbox: один РП = одна папка»
- Смежное: DP.M.010 §7 (governance document split pattern)

*Источник: peer-сессия 2026-05-25-07, git diff 4acbd297*
