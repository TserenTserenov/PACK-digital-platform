---
id: DP.METHOD.191
name: "Cherry-pick recovery: восстановление коммита с чужой ветки при параллельном merge"
type: method
pack: PACK-digital-platform
domain: digital-platform / multi-agent-git-coordination
kind: Method
status: active
created: 2026-07-15
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
sources:
  - "WP-251 пир-сессия 2026-07-10, Решение 6 (commit 86fe38f35)"
related:
  complements: [DP.FM.258]
schema_version: 1
---

# DP.METHOD.191 — Cherry-pick recovery

## Определение

Метод восстановления коммита, случайно созданного на чужой ветке из-за параллельного переключения HEAD другим агентом (DP.FM.258), когда целевая ветка (main) уже продвинулась вперёд без этого коммита.

## IPO

- **Вход:** коммит существует в git-истории (`reflog` или `--all`), но не на target-ветке; target-ветка продвинулась без этого коммита
- **Процесс:** `git log --all --oneline` → найти SHA → переключиться на актуальный main → `git cherry-pick <sha>` → `git push`
- **Выход:** коммит присутствует в main; хуки не повторяются повторно (cherry-pick наследует содержимое исходного коммита)

## Диагностика проблемы (DP.FM.258)

```bash
git log --oneline origin/main         # коммита нет на main
git log --all --oneline | grep <тема> # коммит есть в другом месте
git branch --show-current             # HEAD не на main в момент создания
```

## Цепочка восстановления

```bash
git log --all --oneline               # найти SHA «потерянного» коммита
git checkout main && git pull         # обновить main
git cherry-pick <sha>                 # применить коммит на main
git push origin main                  # отправить
```

## Тест восстановимости

| Состояние | Метод |
|-----------|-------|
| Коммит в `git reflog / --all` | cherry-pick достаточен — данные не потеряны |
| Коммит не в reflog (gc удалил) | ручная реконструкция патча из содержимого |

## Дополнительный контекст

Если main продвинулся через автомерж PR (без потерянного коммита), cherry-pick — единственный способ без rewrite истории.

## Источник

WP-251 (2026-07-10): коммит с решением R15 (accept M-129) ушёл на peer-ветку из-за параллельного `git checkout` от WP-149 между двумя шагами сессии. PR#8 автомержился без этого коммита. Восстановлено через cherry-pick без потери данных. Дополняет DP.FM.258 (описание сбоя) — здесь: метод исправления.
