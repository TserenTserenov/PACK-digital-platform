---
id: DP.FM.250
name: "FETCH_HEAD race: параллельные git pull на одном рабочем дереве задваивают файл"
type: fm
pack: PACK-digital-platform
domain: digital-platform / version-control-safety
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-close 2026-07-10, iwe-server-config commit 1055eb8 (tsekh-timer-race fix)"
schema_version: 1
---

# DP.FM.250 — FETCH_HEAD race: параллельные git pull задваивают файл

**Суть:** Два параллельных `git pull` процесса на одном репозитории одновременно пишут в `.git/FETCH_HEAD`. Файл содержит записи от обоих процессов — включая дублирующиеся «главные» ветки. git видит несколько «главных» веток и отказывается выполнять rebase.

## Механизм

1. systemd-таймер A запускает `git pull --rebase origin main`
2. systemd-таймер B запускает тот же скрипт в ту же секунду
3. Оба процесса пишут в `.git/FETCH_HEAD` без координации
4. Файл содержит дубликат записи `branch 'main' of ...` с пометкой «not-for-merge»
5. git rebase отказывает: «multiple branches marked as 'main'»

## Диагностика

Прямое воспроизведение: запустить оба процесса вручную одновременно → снять содержимое `.git/FETCH_HEAD` в момент сбоя. Если файл содержит дублирующиеся `not-for-merge` записи — подтверждён race condition.

## Усугубляющий фактор

Глобальный `pull.rebase=true` в git config направляет «безопасный» `--ff-only` в rebase-логику, где дублирование FETCH_HEAD вызывает отказ (см. DP.FM.252).

## Фикс

Один общий лок (`flock /tmp/iwe-git-sync.lock`) на весь проход по репозиториям — не per-repo. Оба скрипта итерируют репозитории последовательно, параллелизм существует только между самими скриптами (см. DP.M.372).

## Применимо

Любые два cron/systemd-скрипта, работающих с одним git-репозиторием в одинаковые интервалы.
