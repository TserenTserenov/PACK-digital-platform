---
id: DP.FM.154
name: "Commit-without-push на сервере → отложенная дивергенция"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: ci-cd
severity: high
valid_from: 2026-06-11
related:
  see_also: [DP.FM.070]
tags: [git, commit, push, divergence, headless-agent, cron, server, ci-cd, incomplete-pipeline]
source: "session-transcript 2026-06-11-01-night-errors-triage-fix + git diff iwe-server-config (484821f)"
schema_version: 1
---

# DP.FM.154 — Commit-without-push на сервере → отложенная дивергенция

## Описание

Агенты в серверном окружении (NixOS headless, cron, systemd timer) выполняют `git commit` без последующего `git push`. Commit успешен локально, push не выполняется — нет интерактивного режима, SSH-агент не форвардится, ключ не разблокирован. Результат: локальный HEAD уходит вперёд относительно origin → divergence → watchdog (например, pull-repos) алертит «N коммитов unsynced» каждый цикл проверки.

## Контекст возникновения

- Headless-агент или скрипт на сервере (cron, systemd, launchd)
- Скрипт делает git commit как часть pipeline'а (auto-commit, snapshot)
- Нет explicit `git push` в том же скрипте ИЛИ push сконфигурирован, но не работает (нет ключа, нет агента, прав мало)
- Никто не следит за «здоровьем доставки» отдельно от факта коммита

## Симптом

- Watchdog (pull-repos, git-status-monitor) алертит «N коммитов unsynced» периодически.
- Локальный репо на сервере растёт по объёму ушедших, но не доставленных коммитов.
- При обращении к репо через клиент (рабочая станция, другой агент) — `git pull` неожиданно требует merge или показывает diverged.
- В логах cron/systemd unit'а нет ошибки — commit прошёл успешно, push просто не вызывался.

## Корректные паттерны

**Вариант A — атомарный commit+push в одном скрипте:**
```bash
git add -- <specific-files>
git commit -m "$msg" || exit 0  # nothing to commit — не ошибка
git push origin "$branch" || { log_error "push failed"; exit 1; }
```

**Вариант B — отдельный delivery-сервис (push-ahead):**
- Скрипт A делает только commit.
- Скрипт B (delivery) периодически вызывает `git push` для всех локально ahead-репо.
- Разделение ответственности позволяет независимо мониторить «качество доставки».

**Профилактика:**
- В headless-окружении: явный push в каждом auto-commit скрипте, fail-loud на ошибке push.
- SSH-агент развёрнут под пользователем cron/systemd unit'а.
- Healthcheck «ahead > 0» через 15 мин → алерт (быстрее, чем 2ч pull-repos).

## Диагностика

`for repo in $(find . -type d -name .git); do cd $(dirname $repo); git status -sb | head -1; done` — `ahead N` без `behind` = непушенный commit.

## Тест

«Может ли действие остаться неполным при обрыве после commit?» Да → нужен явный delivery-шаг (push) или отдельный delivery-сервис.
