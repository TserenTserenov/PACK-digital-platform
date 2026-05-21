---
id: DP.FM.059
title: Hook Command Relative Path Silent Fail
kind: FM
status: draft
trust: empirical
epistemic_stage: pattern
created: 2026-05-20
valid_from: 2026-05-20
sources:
  - commit 72f7572 (fix: hook paths — $CLAUDE_PROJECT_DIR/ convention)
  - commit c174fdc9 (DS-ecosystem-development, overnight agent)
related:
  distinguishes_from: []
  references: []
---

# DP.FM.059 — Hook Command Relative Path Silent Fail

## Описание

Хук Claude Code (`settings.json → hooks[].command`) задан с относительным путём к скрипту (`bash .claude/hooks/rule-engine.sh`). При headless-запуске (launchd, cron, subagent, git worktree) рабочая директория отличается от корня проекта → скрипт не найден → хук завершается с exit=0 без ошибки и без работы. Overnight-агент тихо производит 0 результатов.

«Относительный путь в hook-команде работает только из корня проекта в IDE; в любом другом контексте — тихий сбой без диагностики.»

## Условие возникновения

- `settings.json hooks[].command` содержит `bash .claude/hooks/X.sh` (без `$CLAUDE_PROJECT_DIR/` префикса)
- Агент запускается headless: launchd, systemd, cron, subagent, git worktree
- CWD ≠ корень проекта при запуске

## Fix

Все hook-команды в `settings.json` должны использовать `$CLAUDE_PROJECT_DIR/` как префикс:

```json
{
  "command": "bash $CLAUDE_PROJECT_DIR/.claude/hooks/rule-engine.sh"
}
```

`$CLAUDE_PROJECT_DIR` устанавливается Claude Code автоматически и всегда указывает на корень проекта независимо от CWD.

## Тест (триггер распознавания)

«Overnight-агент или scheduled hook не производит вывода? CWD при запуске ≠ корень проекта?» → Да → проверить prefix в `settings.json hooks[].command`. Первый шаг диагностики silent-fail overnight агента — hook paths, не логика скрипта.

## Применимость

- FMT-exocortex-template (шаблонные хуки)
- Любые IWE-инсталляции с overnight-агентами
- CI/CD pipeline с Claude Code hooks
