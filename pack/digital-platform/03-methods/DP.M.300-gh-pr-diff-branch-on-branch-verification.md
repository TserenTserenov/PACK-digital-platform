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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость ревью (`gh pr diff`) ↔ точность scope (checkout + git log) | Один CLI-вызов быстро показывает diff в терминале; полный протокол требует fetch+checkout+несколько git-команд, прежде чем можно судить о реальном содержимом PR |
| Удобство инструмента ↔ ограничение его семантики | `gh pr diff` — удобная обёртка, но её semantics (`branch vs base`) не совпадает с интуитивным ожиданием «только коммиты этого PR»; расхождение проявляется именно на branch-on-branch |
| Доверие к отчёту peer-агента ↔ независимая верификация | Peer-агент может честно заявить «файл удалён»; но без собственной проверки через checkout рецензент опирается на чужой отчёт вместо факта |
| Актуальность ветки (rebase на main) ↔ стабильность истории PR | Rebase перед мержем выравнивает diff с реальностью, но переписывает историю коммитов PR, что может усложнить последующее ревью для тех, кто уже начал его читать |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| «gh pr diff — это же и есть diff PR» | Название команды создаёт ложную уверенность, что вывод изолирует именно коммиты PR; реальная семантика (branch vs base) остаётся непроверенной, пока не встретился branch-on-branch случай |
| «Peer-агент отчитался — значит, проверено» | Готовый отчёт («файл удалён») снижает бдительность рецензента; внимание переключается на согласие с отчётом вместо независимой сверки через checkout |
| «PR выглядит простым — глубокая проверка не нужна» | Оценка сложности PR на глаз (мало файлов, понятное описание) снижает мотивацию запускать полный протокол (fetch+checkout+git log); именно небольшие PR на стеке веток чаще всего проверяются поверхностно |
| «Один взгляд на diff в терминале — этого достаточно» | Терминальный вывод `gh pr diff` воспринимается как окончательный источник истины; альтернативная сверка (`git diff main --name-status` после checkout) откладывается как «избыточная перепроверка» |

## Отличие от соседей

- Capture lines 2692 (fetch+rebase race при push) — про condition после write, не про чтение diff.
- DP.M.291 (patch-object-vs-string-path-mock) — про мокинг, не про PR-review.
