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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Простота вызова скилла как first-class API ↔ контроль capabilities и контекста | `claude -p /skill-name` убирает хранение и передачу prompt-файлов, но без явного `--allowedTools "Read,Write,Edit,Glob,Grep,Bash"` и обязательного `cd "$WORKSPACE"` headless-запуск получает лишние права и теряет разрешение CLAUDE.md |
| Автономность headless-запуска (cron/launchd/systemd) ↔ наблюдаемость и управляемость | Агент работает без человека в цикле, поэтому timeout обязателен (без него зависание некому заметить), а обработка rc (124=timeout, 0=success, иное=fail+WARN) — единственный канал диагностики, который у метода остаётся |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Внимание к имени скилла затмевает окружение вызова | Практик сосредотачивается на `/$skill_name` и забывает, что корректность результата определяется `cd "$WORKSPACE"` и env-переменными (CLAUDE_TIMEOUT, model_override) — скилл «тот же», а разрешение контекста и лимиты другие |
| «Запустилось» принимается за «отработало» | Фокус на самом факте dispatch смещает внимание от содержания stdout агента: без чтения вывода молчаливые сбои внутри успешного запуска не замечаются, а fail+WARN-путь по rc остаётся единственным сигналом, который никто не разбирает |

---

> 2026-07-31 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 4). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
