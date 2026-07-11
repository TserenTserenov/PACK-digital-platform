---
id: DP.FM.244
name: "Staged-delete другого агента поглощается коммитом при multi-agent git-работе"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-29
source: "session 2026-06-29 (WP-451); commit c5a82f491 удалил drafts/D-056, revert 73b95d70f"
related:
  references: [AR.235]
  see_also: ["CLAUDE.md §Agent Core Git Staging: запрет git add -A/-u/."]
tags: [git, multi-agent, staged-delete, commit-isolation, silent-data-loss]
---

# DP.FM.244 — Staged-delete другого агента поглощается коммитом (multi-agent git)

## Паттерн

Агент A выполняет `git rm <file>` (staged delete), но не коммитит сразу. Агент B стейджит свои файлы через `git add <specific-file>` и делает коммит. Поскольку staged delete A уже в общем индексе, `git commit` агента B захватывает и удаление — файл исчезает из репо в коммите B без явного намерения.

## Пример

```bash
# Агент A:
git rm drafts/D-056.md   # staged delete в индексе, не закоммичен

# Агент B (позднее, без проверки индекса):
git add my-new-file.md   # стейджит только свой файл
git commit -m "feat: add my-new-file"
# → в коммите оба изменения: my-new-file создан + D-056 удалён
```

Инцидент 2026-06-29: commit `c5a82f491` удалил `drafts/D-056`, потребовался revert `73b95d70f`.

## Механизм

1. `git rm` пишет staged delete в **общий** индекс репо — не «приватное» действие агента
2. Специфичный `git add <file>` защищает от добавления чужих файлов — НЕ от уже застейдженных удалений
3. `git commit` берёт ВЕСЬ текущий индекс, включая staged deletes от других агентов
4. Без `git diff --cached --name-only` агент B не видит, что чужой delete уже в индексе

## Почему опасен

- **Нет предупреждения**: git не сообщает, что часть коммита принадлежит другому агенту
- **Специфичный `git add` не защищает**: удаление уже в индексе до того, как B начал staging
- **Потеря данных**: черновики/артефакты исчезают без решения их автора
- **Инверсия видимости**: коммит принадлежит B, дефект создал A — диагностика затруднена

## Лечение

1. **Перед каждым `git commit`**: `git diff --cached --name-only` — сверить со списком файлов своей задачи
2. Чужое в индексе (в т.ч. staged delete) → `git restore --staged <file>` до commit
3. Обнаружен post-factum → revert (не rewrite history), см. §Agent Core: NEVER destructive git
4. **Правило AR.235** (commit isolation in shared repo): sanitize индекса обязателен

## Связи

- Правило: AR.235 — профилактика этого FM (sanitize до commit)
- Специализация: Git Staging CRITICAL (CLAUDE.md §Agent Core) — запрет `git add -A/-u/.`
- Примечание: FM срабатывает даже при корректном `git add <file>` — специфичный add не видит staged state других агентов
