---
id: DP.FM.056
title: Deprecated ≠ Удалён — рантайм-скрипты ссылаются на deprecated_files до физического удаления
kind: FM
status: draft
trust: empirical
epistemic_stage: pattern
created: 2026-05-20
valid_from: 2026-05-20
sources:
  - commit e337183 (FMT-exocortex-template validate-template check [8/8])
related:
  distinguishes_from: [DP.FM.010]
  references: []
---

# DP.FM.056 — Deprecated ≠ Удалён: рантайм-скрипты ссылаются на deprecated_files

## Описание

При миграции функциональности в template-системе:
1. Создан новый путь (скилл как параллельная замена)
2. Старый путь помечен в `deprecated_files[]` в `update-manifest.json`
3. Рантайм-скрипты (`strategist.sh`, `scheduler.sh`) ещё читают старый путь

`update.sh` физически удаляет файлы из `deprecated_files[]` → runner падает с «file not found» при следующем запуске. Баг незаметен при ручном использовании (файл ещё существует), проявляется после обновления шаблона.

## Первопричина

«deprecated» (устаревший, но существующий) трактуется как «удалённый». Нарушен инвариант: **deprecated → runner-удалён** = precondition для физического удаления. Без выполнения precondition файл из `deprecated_files[]` удалять нельзя.

## Условие возникновения

- Template-система с manifest-driven lifecycle (`update.sh`)
- Миграция: новый путь создан параллельно старому
- Старый путь добавлен в `deprecated_files[]` до обновления runner-скриптов

## Превентивный контроль

Cross-ref проверка в `validate-template` [8/8]:
- Файл не может одновременно быть в `files[]` и `deprecated_files[]`
- Runner-скрипты не должны ссылаться на пути из `deprecated_files[]`

## Тест (триггер распознавания)

«Помечаю файл в `deprecated_files[]`? → Все runner-скрипты обновлены на новый путь?» Нет → не добавлять в `deprecated_files[]` до обновления runner.

## Применимость

- Template-системы с manifest-driven lifecycle (update.sh, Helm charts, Nix profiles)
- Headless-сценарии, читающие пути из конфигурации (launchd, cron, systemd)

## Связи

- **DP.FM.010** — Legacy Port Jump (смежный: прыжок в новый дизайн без проверки текущего runner)
