---
id: DP.FM.193
title: "Мёртвая копия хука в `.git/hooks/` при установленном `core.hooksPath` не читается git"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / tooling-hygiene
epistemic_stage: confirmed
valid_from: 2026-07-03
source: "session-close 2026-07-03 (DS-my-strategy commit f5c672138)"
related:
  see_also: [DP.FM.085]
---

# DP.FM.193 — Мёртвая копия хука в `.git/hooks/` при установленном `core.hooksPath`

## Описание

При установленном `git config core.hooksPath = <custom-dir>/`, git полностью игнорирует `.git/hooks/`. Если в `.git/hooks/` остаётся старая копия hook'а — она никогда не запустится, но создаёт ложное ощущение «хук активен». При отладке разработчик смотрит `.git/hooks/`, видит файл, думает что hook работает — а реально работает (или не работает) версия из `<custom-dir>/`.

## Симптом

- Хук в `.git/hooks/` существует, но никогда не срабатывает.
- Отладка «есть ли хук» в `.git/hooks/` даёт false-positive: файл есть, но он мёртв.
- commit-msg или pre-commit логика не выполняется, хотя файл присутствует.

## Диагностика

`git config --local core.hooksPath` — если указывает на custom path, `.git/hooks/` мертва полностью.

## Причина

При установленном `core.hooksPath` git исключает всю директорию `.git/hooks/` — это «или/или», не «оба пути».

## Профилактика

1. При установке `core.hooksPath` — удалить соответствующие файлы из `.git/hooks/`.
2. Альтернатива: добавить в `.git/hooks/<name>` stub с комментарием `# dead hook — uses .githooks/`, чтобы явно сигнализировать о редиректе.
3. Тест: `git config --local core.hooksPath` → не пусто? → `.git/hooks/` не читается.

## Тест обнаружения

```bash
git config --local core.hooksPath  # не пусто = custom path активен
ls .git/hooks/                      # файлы есть = FM активен (dead copies)
```

## Связи

- DP.FM.085 (hook-installer anti-patterns) — смежный: installer создаёт dead copies при двойном запуске
