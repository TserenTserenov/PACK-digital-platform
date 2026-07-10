---
id: DP.M.359
name: "Static delta-aware lint for bare-commit guard"
name_ru: "Статический delta-aware линт для защиты от bare commit в agent-skill файлах"
summary: "Grep по изменённым .claude/skills/** и scripts/** на bare `git commit -m` без pathspec; срабатывает только при коммите, вносящем новый bare-commit паттерн. Нет lifecycle, нет стейта, ловит регрессию."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: agent-enforcement
valid_from: 2026-06-20
related:
  see_also: [AR.216, DP.SC.181]
tags: [lint, delta-aware, static-analysis, bare-commit, agent-skill, enforcement]
source: "peer-session 2026-06-20-39, report.md"
schema_version: 1
---

# DP.M.359 — Статический delta-aware линт для защиты от bare commit в agent-skill файлах

## Описание

Метод предотвращает регрессии: появление bare `git commit -m` без pathspec в agent-skill файлах (SKILL.md, scripts/*.sh) через статический grep по дельте коммита.

**Принцип «ловить виновника при коммите, не всех при любом коммите»:** детектор срабатывает только когда именно этот коммит вносит новый bare-commit паттерн в инструмент. Существующий код не затрагивается.

**Альтернативы, отклонённые как более сложные:**
- Семафор lifecycle — требует stateful tracking
- Fail-closed guard на старте — false positives на легитимных bare commits в других контекстах
- Серверный pre-receive hook — требует серверного доступа

## Algorithm

### Step 1: Определи scope изменения

```bash
DELTA_FILES=$(git diff --cached --name-only | grep -E '\.claude/skills/.*\.(md|sh)$|scripts/.*\.sh$')
```

### Step 2: Grep на bare commit паттерн

```bash
for file in $DELTA_FILES; do
  if git show :"$file" 2>/dev/null | grep -qE '^[[:space:]]*git commit -m ["\'"'"'][^"\'"'"']*["\'"'"'][[:space:]]*$'; then
    echo "ERROR: bare git commit (no pathspec) found in $file"
    exit 1
  fi
done
```

### Step 3: Применимость

«Вносит ли этот коммит новый bare `git commit -m` без pathspec в agent-skill файл?»
- Да → блокировать, потребовать pathspec
- Нет → пропустить

## When to use

- При добавлении нового скилла или модификации существующего
- При написании scripts/*.sh для агентных процессов
- Как pre-commit hook в .claude/settings.json PreToolUse или post-commit

## Тест применимости

«Есть ли в дельте agent-skill файла строка `git commit -m "msg"` без имён файлов после?»
- Да → bare commit → блокировать
- Нет → pathspec присутствует → ok

## Связи

- AR.216 (pre-commit staged only) — комплементарное правило: staged-only предотвращает mix-up изменений; данный метод предотвращает bare-commit в коде
- DP.SC.181 — прецедент delta-aware подхода для другого типа артефактов
- AR.D.001 — данный метод является одним из способов «закрепить правило в коде» (code enforcement, а не только memory)
