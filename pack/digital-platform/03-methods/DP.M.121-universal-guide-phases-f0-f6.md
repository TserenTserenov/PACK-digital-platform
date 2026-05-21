---
id: DP.M.121
title: Universal Guide Creation Phases (Ф0–Ф6)
kind: METHOD
status: draft
trust: empirical
epistemic_stage: validated
created: 2026-05-20
valid_from: 2026-05-20
sources:
  - commit 6eed7e4 (DS-principles-curriculum, feat(guide-v4))
related:
  uses: [DP.M.115]
  references: []
---

# DP.M.121 — Universal Guide Creation Phases (Ф0–Ф6)

## Описание

Метод создания универсального руководства (guide-v4) в 7 фазах с явным DoD и обязательным прогоном v4-lint после каждой фазы. Каждая фаза производит проверяемый артефакт.

## Фазы

| Фаза | Название | DoD | Линтер-точка |
|------|---------|-----|-------------|
| Ф0 | Bootstrap | scaffolding + frontmatter | v4-lint: frontmatter valid |
| Ф1 | Структура | оглавление, S1-S6 заголовки | v4-lint: structure valid |
| Ф2 | Введение + Зачем | секции S1-S3 заполнены | v4-lint: intro sections |
| Ф3 | Body-секции | S4-S10, каждая с body-шаблоном | v4-lint: body-template present |
| Ф4 | Специализация | platform-specific блоки добавлены | v4-lint: specialization |
| Ф5 | Peer review | отдельный агент проверил | v4-lint: pass + peer sign-off |
| Ф6 | Merge + публикация | PR merged, CI green | — |

## Ключевые инварианты

- body-шаблон единен для всех S7–S10: несоответствие = структурный разрыв, блокирует следующую фазу
- v4-lint проверяет только формальные критерии (frontmatter, H2, body-template): «ошибок нет» ≠ «контент качественный"
- Смысловое ревью делает peer-agent (Ф5), не линтер

## Применимость

Метод универсален для любого контента с фиксированной структурой секций: руководства, учебные программы, онбординг-паттерны.

## Отличие от ad-hoc написания

Каждая фаза даёт артефакт, проверяемый независимо. Ad-hoc: полный документ только в конце, невозможно проверить промежуточное состояние.
