---
id: DP.FM.252
name: "git pull.rebase=true глобально переопределяет --ff-only, направляя команду в rebase-логику"
type: fm
pack: PACK-digital-platform
domain: digital-platform / version-control-safety
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-close 2026-07-10, iwe-server-config commit 1055eb8 (tsekh-timer-race fix)"
see_also: [DP.FM.228]
schema_version: 1
---

# DP.FM.252 — Global pull.rebase=true overrides --ff-only

**Суть:** `git config --global pull.rebase=true` имеет приоритет над флагом `--ff-only` в части стратегии слияния. Команда `git pull --ff-only origin main` выглядит безопасной, но на системе с глобальным `pull.rebase=true` переходит в rebase-логику.

## Механизм

```bash
# На системе с: git config --global pull.rebase=true

git pull --ff-only origin main
# Ожидание: fast-forward или отказ
# Реальность: git входит в rebase-логику, игнорируя --ff-only
```

## Проблема

Скрипт, написанный «безопасно» с `--ff-only`, ведёт себя по-другому в зависимости от глобального окружения. Особенно опасно в cron/systemd-скриптах, где окружение отличается от интерактивного shell разработчика.

## Диагностика

```bash
git config --global --get pull.rebase  # проверить глобальный override
```

## Фикс

Явный `--no-rebase` или `-c pull.rebase=false` в команде:

```bash
git -C "$repo" pull --ff-only --no-rebase origin main
# или
git -c pull.rebase=false pull --ff-only origin main
```

## Принцип

Инфраструктурные скрипты не должны полагаться на дефолты — явно задавать каждый параметр поведения, влияющий на безопасность операции.

## Применимо

Скрипты, выполняющиеся в системном окружении (cron, systemd, CI), где глобальный git config может отличаться от разработческого окружения.
