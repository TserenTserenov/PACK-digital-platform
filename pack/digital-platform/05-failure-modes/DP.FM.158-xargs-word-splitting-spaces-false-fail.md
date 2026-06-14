---
id: DP.FM.158
title: "xargs word-splitting on filenames with spaces causes false-FAIL"
name_ru: "xargs разбивает имена файлов с пробелами на несуществующие пути — ложный отказ"
name_en: "xargs word-splitting false-FAIL on IWE filenames with spaces"
summary: "Использование pipe-xargs для проверки существования файлов с пробелами в имени: «DayPlan 2026-06-13.md» разбивается на «DayPlan» и «2026-06-13.md» — оба несуществующих → false FAIL на каждом Day Close."
type: failure-mode
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: 3
valid_from: 2026-06-13
related:
  see_also: []
source: "git diff FMT-exocortex-template/.claude/hooks/day-close.sh, 2026-06-13 (commit 4cf41d7, PR #173)"
---

# DP.FM.158 — xargs word-splitting on IWE filenames with spaces

## Краткое описание

`find ... | xargs test -f` разбивает строку по пробелам. «DayPlan 2026-06-13.md» трактуется как два аргумента: «DayPlan» и «2026-06-13.md» — оба не существуют → exit 1 (false FAIL) на каждом Day Close.

## Симптомы

- Day Close / Week Close завершается с ошибкой, хотя DayPlan существует.
- В shell-трассировке виден `test -f DayPlan` (без даты) → exit 1.
- Ошибка стабильная (каждый запуск), не intermittent.

## Контекст

IWE-именование содержит пробелы по архитектурному решению:
- `DayPlan YYYY-MM-DD.md`
- `WeekReport W{N} YYYY-MM-DD.md`
- Русскоязычные имена файлов

Применяется везде, где имена файлов имеют пробел и хук использует pipe+xargs для проверки.

## Дополнительный дефект

Substring false-pass: числовое совпадение — «1» найдено в «31» / «12» / «21» при grep без bounded tokens. Фикс: `DAY([^0-9]|$)` вместо `DAY`.

## Решение

```bash
# Вместо:
echo "DayPlan ${date}.md" | xargs test -f

# Правильно:
file="DayPlan ${date}.md"
test -f "$file"
```

Или через glob: `compgen -G "DayPlan *.md"`.

## Связи

- Применимо ко всем IWE-хукам, проверяющим наличие файлов с пробелами в именах
