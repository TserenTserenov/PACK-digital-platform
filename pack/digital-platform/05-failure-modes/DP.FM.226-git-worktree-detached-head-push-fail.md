---
id: DP.FM.226
name: "git worktree add из remote-ветки создаёт detached HEAD — push без явного refspec падает"
type: fm
pack: PACK-digital-platform
domain: digital-platform / version-control-safety
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-close 2026-07-06, FMT-exocortex-template translate-sync CI (commit e8c6d2e)"
schema_version: 1
see_also: [DP.FM.212, DP.FM.213]
---

# DP.FM.226 — git worktree detached HEAD: push без явного refspec падает

**Суть:** `git worktree add ../dir origin/branch` при уже существующей remote-ветке создаёт worktree в detached HEAD (не привязан к локальной ветке). `git push origin branch` из такого worktree падает с ошибкой.

## Механизм

```
git worktree add ../en-draft origin/en-draft
# → detached HEAD: HEAD указывает на commit, не на ветку

git push origin en-draft   # FAIL: ветка не прикреплена к HEAD
git push origin HEAD:en-draft  # OK: явный refspec
```

## Фикс

Всегда использовать `HEAD:<branch>` refspec при push из worktree, созданного через `git worktree add ... origin/branch`.

## Применимость

CI-пайплайны с `git worktree add` из remote-ветки.
