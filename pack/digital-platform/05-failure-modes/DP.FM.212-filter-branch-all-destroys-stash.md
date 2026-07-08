---
id: DP.FM.212
name: "git filter-branch --all уничтожает refs/stash (multi-parent)"
type: fm
pack: PACK-digital-platform
domain: digital-platform / version-control-safety
trust: draft
epistemic_stage: observed
valid_from: 2026-07-07
source: "session-close 2026-07-04 (WP-399 — exocortex/lessons_filter_branch_all_rewrites_stash.md)"
related:
  see_also: [DP.FM.213, DP.FM.026]
---

# DP.FM.212 — git filter-branch --all уничтожает refs/stash

## Описание

`git filter-branch ... -- --all` разворачивает `--all` в «все refs», включая `refs/stash`. Generic-рерайт не сохраняет multi-parent структуру stash-коммита: после рерайта `git stash pop` падает с `fatal: 'stash@{0}' is not a stash-like commit`.

## Механизм

Stash-коммит имеет 2-3 родителя (HEAD-снимок, индекс-снимок, untracked-снимок). Стандартный generic-рерайт обрабатывает все refs как обычные коммиты и не воспроизводит специальную multi-parent структуру stash.

## Симптомы

```
fatal: 'stash@{0}' is not a stash-like commit
```

## Диагностика и фикс

- **Диагностика:** `git stash list` — есть записи, но `git stash pop` падает
- **Удалить битую запись:** `git update-ref -d refs/stash` (не `git stash drop` — та тоже падает)
- **Превентивно:** перед filter-branch сделать `git stash pop` или `git stash drop`

## Правило

Тест: «Есть активный stash и нужно переписать историю?» — указывать явную ветку (`-- main`), не `-- --all`.

## Связи

- DP.FM.213 (worktree-sync при filter-branch) — смежная опасность при рерайте текущей ветки
- DP.FM.026 (.env в git history) — типичный сценарий, при котором применяется filter-branch
