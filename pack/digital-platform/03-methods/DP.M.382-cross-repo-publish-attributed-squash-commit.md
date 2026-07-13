---
id: DP.M.382
name: Squash-коммит с атрибуцией автора при публикации в публичный репо
type: method
domain: digital-platform / cross-repo-publishing / git-attribution
status: draft
valid_from: 2026-07-13
sources:
  - session-close 2026-07-09, WP-415 shakedown (commits 4dea62e, 41c20fe, d729333)
related:
  complements: [DP.M.104, DP.M.105]
tags: [git, attribution, squash-commit, reusable-workflow, cross-repo-publish, github-actions, bot-attribution]
---

# DP.M.382 — Squash-коммит с атрибуцией автора при публикации в публичный репо

## Суть метода

При публикации контента из приватного источника (en-draft, перевод, генерация) в публичный репо — вместо переноса полной истории коммитов создавать **один сквошированный коммит** с атрибуцией реального автора-человека (не бота).

Reusable GitHub workflow служит **единой точкой атрибуционной логики** — не дублировать её в каждом вызывающем репо.

## Проблема

Прямой push истории из приватного репо в публичный:
- Засоряет публичную историю промежуточными коммитами
- Атрибутирует коммиты техническому боту (github-actions[bot]), а не человеку-автору
- `git blame` публичного репо показывает бота, не источник

## Алгоритм

1. В reusable workflow: получить изменения из source-репо.
2. Создать одиночный коммит (`git commit-tree`) с явной атрибуцией:
   ```bash
   GIT_AUTHOR_NAME="Human Author"
   GIT_AUTHOR_EMAIL="human@example.com"
   git commit-tree HEAD^{tree} -p HEAD -m "publish: <description>"
   ```
3. Запушить этот один коммит в публичный репо.
4. Логику атрибуции держать ТОЛЬКО в reusable workflow — не дублировать в каждом вызывающем repо.

## Преимущества

- Публичная история чистая: один коммит на публикацию
- `git blame` показывает реального автора
- При изменении логики атрибуции — правится в одном месте (reusable workflow)

## Тест

«Кто является автором в `git log --oneline` публичного репо?» Человек-автор → метод применён. Бот → метод не применён.

## Инцидент

WP-415 shakedown: pipeline изначально пушил полную историю en-draft с авторством бота (github-actions[bot]) в iwesys/iwe-template. Два бага исправлены за один shakedown: (1) атрибуция → человеку, (2) логика вынесена в reusable workflow.

## Связи

- [DP.M.104](DP.M.104-cross-repo-publication-pipeline.md) — cross-repo pipeline с PR-gate (дополняет: editorial review аспект)
- [DP.M.105](DP.M.105-workflow-call-orchestration.md) — workflow_call orchestration (дополняет: CI/CD структура)
- [DP.SC.189](../08-service-clauses/) — translate-sync сервис (реализация, где применялся метод)
