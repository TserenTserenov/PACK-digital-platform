---
id: DP.FM.211
type: failure-mode
title: "Неполный набор .gitignore паттернов: *.env не покрывается .env.*"
pack: PACK-digital-platform
domain: platform-tooling / security
status: draft
valid_from: 2026-07-07
source: "session-close 2026-07-03 (IWE f332df7, fix(gitignore))"
schema_version: 1
related:
  see_also: [DP.FM.026, DP.FM.138]
---

# DP.FM.211: Неполный набор .gitignore паттернов для env-файлов

## Описание

Паттерн `.env.*` покрывает файлы вида `.env.production`, `.env.local` — с ведущей точкой и суффиксом. Он НЕ покрывает файлы с суффиксом `.env` без ведущей точки: `railway.env`, `something.env`. Без паттерна `*.env` такие файлы не попадают в .gitignore и могут быть случайно закоммичены с токенами.

## Класс дефекта

Тихое включение secrets в репозиторий: файл попадает в `git add .` без предупреждения, не задетектирован .gitignore.

## Полный минимальный набор паттернов

```
.env          # точное имя .env
.env.*        # .env.local, .env.production
*.env         # railway.env, something.env — НЕ покрывается .env.*
```

Все три необходимы и дополняют друг друга.

## Диагностика перед push

```bash
git ls-files | grep -iE "\.env$|^\.env$|\.env\."
```

Если возвращает результаты — проверить намеренность.

## Связи

DP.FM.026 (.env в git history — ликвидация после утечки) — что делать ПОСЛЕ попадания в историю. Этот FM — превентивный: неполный набор паттернов как причина.
