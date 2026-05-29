---
id: DP.M.203
type: method
name: Neon multi-DB FDW cross-schema prefix rules
pack: PACK-digital-platform
domain: digital-platform
trust: high
epistemic_stage: validated
valid_from: 2026-05-28
source: session-transcript 2026-05-28, WP-327 v4.4 migration-244 incident
---

# DP.M.203 — Neon multi-DB FDW cross-schema prefix rules

## Метод

Правила именования схем при кросс-DB запросах в Neon multi-DB архитектуре с FDW (DP.ARCH.004):

| Контекст запроса | Правильный префикс |
|---|---|
| Внутри БД-хозяина | `public.` или без префикса |
| Из другой БД через FDW | `_foreign_<source_db>.имя_таблицы` |
| Никогда | `<source_db>.` напрямую |

Схема с именем `<source_db>` не создаётся автоматически в FDW-хосте. FDW-схема всегда имеет префикс `_foreign_`.

## Pre-apply gate

Перед запуском миграции с кросс-DB join'ами:

```sql
-- В target DB
\dn
\dt _foreign_*.*
```

Проверить наличие нужных FDW-схем. Если `_foreign_<source_db>` не найден → FDW не настроен или имеет другое имя.

## Паттерн дрейфа

SQL-файл в репо написан с `reference.` (неверно), в production функция использует `_foreign_reference.` (верно, но исправлено post-deploy неформально). Это создаёт undocumented correction, невидимую при code review.

## Связи

- Архитектура: DP.ARCH.004 (Neon multi-DB с FDW)
- Урок: `memory/lessons_wp327_v44_apply.md`
