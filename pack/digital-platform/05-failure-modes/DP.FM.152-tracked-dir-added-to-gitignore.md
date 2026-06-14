---
id: DP.FM.152
name: "tracked-dir-added-to-gitignore: Добавление отслеживаемой git-папки в .gitignore без untracking"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: git-operations
severity: high
valid_from: 2026-06-11
related:
  see_also: [DP.FM.146]
tags: [git, gitignore, tracked-files, watchdog, auto-commit, dirty-check]
source: "session 2026-06-11, session-transcript 2026-06-11-01-night-errors-triage-fix (тема #2, auditor/)"
schema_version: 1
---

# DP.FM.152 — tracked-dir-added-to-gitignore

## Описание

Папка, уже отслеживаемая git (tracked), добавляется в `.gitignore` как предполагаемый «мусор». Git не перестаёт отслеживать уже добавленные файлы — они остаются в индексе и продолжают попадать в коммиты. Изменения внутри папки перестают отображаться в `git status` → watchdog или dirty-check ошибочно считает папку «чистой».

## Контекст возникновения

- Диагноз «грязная папка» интерпретируется как «забытый .gitignore»
- Первый шаг — добавить в .gitignore — без проверки, отслеживается ли папка
- Watchdog мониторит `git status --short` и считает tracked+ignored папку невидимой

## Профилактика

**Первый шаг при диагнозе «лишняя папка»:**
```bash
git ls-files --error-unmatch <папка> 2>/dev/null && echo "TRACKED" || echo "UNTRACKED"
# или
git status --short <папка>
```

**При git-tracked папке с auto-commit:**
- Не добавлять в .gitignore
- Исключить папку из dirty-проверки watchdog'а (`--exclude-dir` или separate logic)
- Или добавить `git rm --cached -r <папка>` ДО записи в .gitignore (атомарный коммит)

## Правильный фикс

При git-tracked auto-commit папке правильное решение: исключить из dirty-проверки watchdog'а, а не из git-контроля версий.
