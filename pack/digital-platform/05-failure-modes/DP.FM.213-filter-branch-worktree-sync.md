---
id: DP.FM.213
name: "git filter-branch на текущей ветке синхронизирует worktree — файлы физически удаляются"
type: fm
pack: PACK-digital-platform
domain: digital-platform / version-control-safety
trust: draft
epistemic_stage: observed
valid_from: 2026-07-07
source: "session-close 2026-07-04 (WP-399 — exocortex/lessons_filter_branch_worktree_sync.md)"
related:
  see_also: [DP.FM.212, DP.FM.026]
---

# DP.FM.213 — git filter-branch на текущей ветке удаляет файлы из worktree

## Описание

При запуске `git filter-branch` на текущей checked-out ветке файл, вычищенный фильтром из истории, реально исчезает с диска — не только из индекса git.

## Механизм

Git после filter-branch синхронизирует worktree с переписанным деревом коммитов. Если файл вычищен из всей истории, git воспринимает его как «удалённый в новой версии HEAD» и физически удаляет.

## Симптомы

Рабочий файл (например, `.env`, `.exocortex.env`) пропадает с диска немедленно после filter-branch без предупреждения. Потеря данных если не было backup.

## Правило

Тест: «Рерайт запускается на текущей checked-out ветке и нужно сохранить рабочую копию вычищаемого файла?» — сначала checkout другую ветку, затем рерайтить нужную.

## Альтернатива

`git filter-repo` явно разделяет рерайт и worktree, поведение безопаснее filter-branch.

## Связи

- DP.FM.212 (stash destruction при --all) — смежная опасность в том же сценарии
- DP.FM.026 (.env в git history) — типичный сценарий применения filter-branch
