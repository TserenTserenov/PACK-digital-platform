---
id: DP.M.300
name: "gh pr diff branch-on-branch: проверка реального scope PR через checkout"
name_ru: "Проверка реального состава PR на ветке-поверх-ветки"
name_en: "gh pr diff branch-on-branch verification"
summary: "gh pr diff на ветке поверх feature-ветки показывает изменения обеих суммарно; реальный scope PR берётся через checkout + git log main..HEAD."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: peer-review
valid_from: 2026-06-09
related:
  see_also: [DP.M.291, DP.SC.154]
tags: [pr-review, gh-cli, git, branch-on-branch, stacked-pr, peer-agent, verification, diff]
source: "session 2026-06-09, WP-403 KE, PR #164/#166 в FMT-exocortex-template + memory/lessons_peer_pr_verification.md"
schema_version: 1
---

# DP.M.300 — gh pr diff branch-on-branch verification

## Описание

`gh pr diff <N>` показывает `<ветка PR> vs <base>`, не изолируя коммиты самого PR. Когда ветка PR основана на feature-branch (не на main), diff отражает изменения обеих веток суммарно — файлы, удалённые в PR, всё ещё видны как `new file` от parent-ветки.

## Симптом

Peer-агент отчитался «✅ файл удалён», но `gh pr diff <N>` показывает файл как `new file`. Вывод «не удалён» — ложный.

## Корректный протокол

1. `git fetch origin <branch>` + `git checkout <branch>` — взять ветку локально.
2. `git log main..HEAD --oneline` — только коммиты этой ветки.
3. `git diff main --name-status` — реальное состояние vs main.
4. `git show <sha> --name-status` — что делает каждый коммит.

## Когда ветка отстала от main

Признак: в `git diff main` файлы из main показаны как `-`-удаления (CLAUDE.md, setup.sh). Действие: `git rebase main` + `git push --force-with-lease` перед мержем.

## Чек-лист ревью peer-PR

- `git diff main --name-status` = ожидаемый набор файлов
- нет scope creep
- ветка не отстала от main
- каждый коммит делает только то, что описывает

## Применимость

Любое ревью PR от peer-агента, особенно branch-on-branch (stacked PRs) — частый паттерн в feature-flag разработке.

## Отличие от соседей

- Capture lines 2692 (fetch+rebase race при push) — про condition после write, не про чтение diff.
- DP.M.291 (patch-object-vs-string-path-mock) — про мокинг, не про PR-review.
