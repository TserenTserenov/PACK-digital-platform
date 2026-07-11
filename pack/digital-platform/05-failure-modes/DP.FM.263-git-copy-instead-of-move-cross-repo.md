---
id: DP.FM.263
type: failure-mode
title: Git-copy вместо git-move при миграции между репо — файлы молча дублируются неделями
trust: observed
epistemic_stage: confirmed
domains: [git, repo-migration, cross-repo, file-management]
source_session: 2026-07-07 session-close (peer-session 2026-07-07-04-repo-cleanup-audit, WP-442)
valid_from: 2026-07-07
schema_version: 1
---

# DP.FM.263 — Git-copy вместо git-move → молчаливое дублирование файлов на недели

## Симптом

При переносе директории из одного репо в другой файлы копируются (`cp -r` + `git add`), но не удаляются из источника. Git не отслеживает «перемещение» между репо — только добавление в целевом и наличие в исходном. Файлы молча дублируются без видимого сигнала.

Реальный кейс (WP-442): 49 файлов drafts/ дублировались две недели.

## Корень

Нет явного шага удаления из источника в рамках той же операции. «Перенёс» по факту означало только «скопировал».

## Профилактика

**Правило:** при миграции файлов между репо — два коммита подряд:
1. `git add <target>` + commit с `feat: migrate <path> to <target-repo>`
2. `git rm -r <source-path>` + commit с `chore: remove source after migration to <target-repo>`

Тест после: `git ls-files <src-path>` в исходном репо должен вернуть пусто.

## Применимо к

- Переносы директорий между DS-репо (drafts, content, docs)
- Cross-repo рефакторинг, где оба репо остаются живыми
