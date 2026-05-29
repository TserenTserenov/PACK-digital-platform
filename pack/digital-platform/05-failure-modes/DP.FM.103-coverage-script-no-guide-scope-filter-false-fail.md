---
id: DP.FM.103
type: fm
name: Coverage-скрипт без фильтра scope агрегирует FAIL из соседних guide
status: draft
trust: high
epistemic_stage: empirical
valid_from: 2026-05-28
sources: [session-transcript 2026-05-28, WP-362-Ф3]
pack: PACK-digital-platform
---

# DP.FM.103 — Coverage-скрипт без фильтра scope агрегирует FAIL из соседних guide

## Симптом

FAIL или WARN на концепт, явно «чужой» для проверяемого guide. Концепт введён в другом guide (eg. 1-3), но появляется как L1 FAIL в guide 1-1.

## Причина

Скрипт concept-coverage не принимает `guide_id` как параметр и агрегирует данные по всей корневой директории guides/ — смешивает coverage разных guide.

## Диагностика

grep концепта по директории конкретного целевого guide: если вхождений нет, а WARN/FAIL есть → скрипт без фильтра.

## Решение

Передавать `guide_id` (директорию) как параметр скрипту; аудировать только файлы внутри scope.

## Применимость

Любые lint/coverage инструменты, работающие по корневой директории вместо заданного scope.
