---
id: DP.FM.133
title: "Backup-restore затирает живое state — backup без 3-way merge"
type: failure-mode
pack: DP
tags: [backup, restore, state, regression, silent-revert, 3-way-merge, git]
status: active
valid_from: 2026-06-05
schema_version: 1
---

# DP.FM.133 — Backup-restore затирает живое state (backup без 3-way merge)

## Симптом

«Фичу делал на прошлой неделе, сегодня её нет». `git bisect` ведёт к коммиту с пометкой `backup` или `restore`. State-файл (yaml/json/config) возвращён к содержимому на момент бэкапа, обновления между snapshot и restore — потеряны.

## Причина

Backup-операция фотографирует state-файл в момент T0 и коммитится. Между T0 и моментом restore (T1) пилот или агент обновляет тот же state-файл напрямую. При restore без 3-way merge backup-снапшот **затирает** обновления T0→T1 — silent revert без конфликта.

Концептуально: backup-restore трактуется как «откат», а не как «слияние ветвей». Для mutable state нужна 3-way merge семантика (base = T0, ours = T1-current, theirs = T0-backup).

## Пример (WP-356, 2026-06-05)

| Файл | T0 (backup 2 июня) | T1 (5 июня перед restore) | После restore без merge |
|------|---------------------|---------------------------|--------------------------|
| `process-catalog.yaml` | 8 процессов | 10 процессов | 8 процессов (-2 потеряны) |
| `day-rhythm-config.yaml` | без `daily_checkpoint_wps` | с полем `daily_checkpoint_wps` | без поля (потеряно) |

Регрессии обнаружены при close РП → потребовалась отдельная фиксация (commit 9be3a104b: +192 / +72 строк восстановления).

## Митигации

1. **Бэкап через `git stash` или branch** — restore = `git merge backup-branch` (явный merge с конфликтами).
2. **Pre-restore diff-gate** — `git diff backup-commit HEAD -- <state-paths>` перед restore; non-empty diff → отказ с сообщением «state изменился между snapshot и restore, нужен manual merge».
3. **Исключить mutable state из бэкапа** — бэкапить только immutable artifacts (docs, schemas), mutable state хранить в Neon/SQLite с собственной репликацией.

## Тест

«После restore возникает diff с предыдущим HEAD по state-файлам?» Да → регрессия гарантирована для всего, что было изменено между snapshot и restore.

## Профилактика

При проектировании backup/restore процедур для exocortex state, derived index files, mutable configs — явно выбрать одну из митигаций. Дефолт «копируем файлы из backup поверх» = silent revert.

## Источник

session-transcript 2026-06-05-13-actualize-wp-356 (Claude + Kimi); git diff DS-my-strategy commit 9be3a104b
