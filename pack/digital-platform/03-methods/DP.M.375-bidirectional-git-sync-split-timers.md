---
id: DP.M.375
type: method
title: bidirectional-git-sync-split-timers — разделение pull и push в независимые периодические юниты
kind: Method
pack: PACK-digital-platform
domain: digital-platform / infra-patterns
trust: observed
epistemic_stage: confirmed
domains: [git-sync, systemd, infra, periodic-sync, webhook-less]
source_session: 2026-07-07 session-close (git diff iwe-server-config, iwe-pull-repos + iwe-push-ahead units)
valid_from: 2026-07-07
schema_version: 1
related:
  see_also: [DP.M.023, DP.M.372]
---

# DP.M.375 — Двунаправленная git-синхронизация через раздельные pull/push таймеры

## Определение

Паттерн периодической двунаправленной git-синхронизации сервера без webhook-инфраструктуры: два независимых timer unit — один для `pull` (входящие), один для `push` (исходящие).

## Когда применять

- Сервер без постоянного публичного endpoint (нет возможности принимать webhook)
- Репозитории на сервере изменяются как локальным агентом, так и удалённо (коллегами/другими агентами)
- Требуется надёжная периодическая синхронизация без риска потери изменений

## Структура

```
unit-1: pull-repos
  trigger: каждые T (напр. 2h)
  action: git pull --rebase по каждому репозиторию

unit-2: push-ahead  
  trigger: каждые T (напр. 2h, offset +N min через DP.M.023)
  action: git push накопленных локальных коммитов
```

## Ключевые свойства

1. **Раздельные юниты:** pull-failure и push-failure независимы. Если pull упал (конфликт) — push продолжает работать. Монолитный pull+push в одном юните обрывается при первом fail.
2. **Разные failure modes:** pull-fail = входящий конфликт (требует ручного merge); push-fail = нет connectivity или rejected. Разные алерты, разные recovery.
3. **Комбинировать с DP.M.023:** fixed-offset между таймерами предотвращает одновременный запуск (timer-race).
4. **Комбинировать с DP.M.372:** если несколько юнитов работают с одним репозиторием — flock на весь проход репо предотвращает FETCH_HEAD задвоение.

## Применимо к

- Любой сервер с git репо без постоянного публичного endpoint (edge nodes, dev-серверы, CI-менее окружения)
- Пример: systemd timer units на Linux/macOS; эквивалентно cron jobs, launchd, Kubernetes CronJob

## Связано

- DP.M.023 — timer-chaining-fixed-offset (предотвращение race между таймерами)
- DP.M.372 — flock-single-pass-lock-granularity (блокировка при конкурентном доступе к репо)
