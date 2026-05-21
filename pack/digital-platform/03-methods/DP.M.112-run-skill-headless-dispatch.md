---
id: DP.M.112
type: method
name: "run_skill() — headless dispatch скиллов через claude -p"
domain: digital-platform
status: draft
trust: medium
epistemic_stage: instance
source: "FMT-exocortex-template commit 333c83d, feat(strategist): complete runner migration"
valid_from: 2026-05-20
---

# run_skill() — headless dispatch скиллов через claude -p

## Суть

Паттерн запуска Claude Code скилла как headless CLI-команды из shell-скрипта:
`claude -p /skill-name` вместо хранения и передачи prompt-файла.

Промпт-файлы нужны только для скиллов, у которых ещё нет slash-команды.

## IPO

**Вход:** имя скилла (skill_name), рабочий контекст ($WORKSPACE), опциональный model_override
**Процесс:**
1. `cd "$WORKSPACE"` — для правильного разрешения CLAUDE.md
2. `timeout "$CLAUDE_TIMEOUT" "$CLAUDE_PATH" --allowedTools "Read,Write,Edit,Glob,Grep,Bash" -p "/$skill_name"`
3. Обработка rc: 124=timeout, 0=success, иное=fail+WARN

**Выход:** stdout агента; rc для диагностики

## Правила

1. `cd "$WORKSPACE"` перед вызовом обязателен — контекст разрешения CLAUDE.md
2. `--allowedTools` явно ограничивает capabilities (безопасность headless-запуска)
3. model_override через env-переменную (IWE_STRATEGIST_MODEL), не хардкод
4. Timeout обязателен — headless агент без него может зависнуть

## Применение

Любой headless cron/launchd/systemd агент на Claude Code CLI.
Вместо хранения промптов в template-репо — вызывать скиллы как first-class API.

## Связи

- Реализация: FMT-exocortex-template/scripts/strategist.sh (commit 333c83d)
- Сервис: DP.SC.135 (Agent Inbox dispatcher)
