---
id: DP.FM.210
type: failure-mode
title: "zsh no-word-split + BSD grep multiline-as-alternatives → always-true guard"
pack: PACK-digital-platform
domain: platform-tooling
status: draft
valid_from: 2026-07-07
source: "session-close 2026-07-03 (IWE e510f76, fix(day-open.checks))"
schema_version: 1
related:
  see_also: [DP.FM.208, DP.FM.033]
---

# DP.FM.210: zsh no-word-split + BSD grep multiline-as-alternatives → false-green guard

## Описание

При использовании `for var in $multiline_var` в zsh: unquoted expansion НЕ делает word-split (в отличие от bash). Плюс BSD grep (macOS) при получении многострочного паттерна в `-e` объединяет строки как список альтернатив `(-e "WP-1\nWP-2" → "WP-1 OR WP-2 OR ...")`. Вместе это даёт guard, который всегда возвращает ✅ независимо от содержимого файла.

## Класс дефекта

Тихий false-green: guard не выдаёт ошибку, сигнализирует «всё хорошо» при любых данных.

## Ситуация возникновения

- Shell: zsh (macOS default, в отличие от bash)
- Паттерн: `for wp in $WPS` где `$WPS` — многострочная переменная (несколько WP через `\n`)
- grep: BSD grep с многострочным паттерном — трактует как alternatives, всегда находит совпадение

## Механизм

1. zsh не разделяет `$var` по пробелам/переводам строк без `(f)` или `${=var}` → вся переменная передаётся как один аргумент
2. BSD grep получает один многострочный паттерн → интерпретирует как диапазон чередования → всегда находит совпадение

## Фикс

```bash
# Было (сломано на zsh + BSD grep):
for wp in $WPS; do
  grep -q "$wp" "$DAYPLAN" || echo "MISSING: $wp"
done

# Стало (одинаково работает в bash и zsh):
while IFS= read -r wp; do
  grep -q "$wp" "$DAYPLAN" || echo "MISSING: $wp"
done <<< "$WPS"
```

## Диагностика

Признак: guard всегда возвращает зелёный, даже при пустом/шаблонном плане. Тест: убрать все РП из целевого файла, запустить guard — если ✅, баг не исправлен.

## Обобщение

При переносе shell-скриптов bash→zsh или тестировании на macOS: явно проверять word-split и флаги grep. Поведение «всегда true» при zsh+BSD grep — классический тихий false-green.

## Связи

DP.FM.208 (macOS bash 3.2 IFS tab nosplit) — тот же класс ограничений macOS/zsh, другой симптом: word-split в for-loop vs IFS в read.
