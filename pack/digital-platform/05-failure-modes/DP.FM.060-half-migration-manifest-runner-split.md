---
id: DP.FM.060
title: Half-Migration Manifest-Runner Split
kind: FM
status: draft
trust: empirical
epistemic_stage: pattern
created: 2026-05-20
valid_from: 2026-05-20
sources:
  - commit 333c83d (FMT-exocortex-template, feat(strategist): complete runner migration)
related:
  distinguishes_from: []
  references: []
---

# DP.FM.060 — Half-Migration Manifest-Runner Split

## Описание

Failure mode «полумиграция»: deprecated-пометка в manifest без обновления runner. Prompt-файлы помечены устаревшими, но scheduled automation (runner-скрипт) продолжает их вызывать. При удалении deprecated prompt-файлов — тихий сбой расписания.

«Окно сбоя между двумя коммитами: manifest deprecated → runner still reads old file.»

## Условие возникновения

- Миграция scheduled automation с prompt-files на skills (или другое переименование точек вызова)
- Manifest и runner обновляются в разных коммитах
- Scheduled задачи не проверяются после первого коммита

## Fix

При миграции scheduled automation обновить manifest + runner в **одном коммите**. Паттерн: `run_skill()` вызывает `claude -p /skill-name` — новый стандарт для headless scheduled automation.

```bash
# было
run_prompt "day-open"
# стало
run_skill "day-open"
```

## Тест (триггер распознавания)

«Помечаю что-то deprecated в manifest? Есть ли runner/скрипт, который читает этот же путь?» → Да → обновить runner в том же коммите.

## Применимость

- FMT-exocortex-template: migration prompt-files → skills
- Любая миграция scheduled automation с manifest как реестром
- CI/CD pipeline version bumps с разделёнными manifest и runner
