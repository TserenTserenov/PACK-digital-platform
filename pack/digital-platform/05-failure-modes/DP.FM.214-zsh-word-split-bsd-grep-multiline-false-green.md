---
id: DP.FM.214
type: failure-mode
title: "zsh unquoted variable не делает word-split + BSD grep трактует многострочный паттерн как список альтернатив → тихий false-green"
pack: PACK-digital-platform
domain: platform-tooling
status: draft
valid_from: 2026-07-07
source: "session-close 2026-07-04 (WP-7 DOSCAF1, DS-my-strategy exocortex/lessons_zsh_bash_wordsplit_grep.md)"
schema_version: 1
related:
  see_also: [DP.FM.208]
---

# DP.FM.214: zsh word-split + BSD grep multiline → тихий false-green

## Описание

Два независимых бага, возникающих вместе на macOS/zsh и дающих тихий false-green:

1. В zsh unquoted `$VARIABLE` с многострочным содержимым **не делает word-split** — весь список передаётся в `for` как один элемент.
2. BSD grep (macOS) с многострочным паттерном (через `\n` или heredoc) **трактует каждую строку как отдельную альтернативу** и матчится по любому вхождению.

Совместное действие: цикл проходит один раз (один «элемент»), а grep всегда возвращает match — проверка выглядит успешной при любых данных.

## Класс дефекта

Тихий false-green: скрипт не выдаёт ошибку, все assert-и проходят, хотя реальная проверка (все ли WP есть в плане) не выполняется.

## Ситуация возникновения

```zsh
# Проблемный код (zsh):
WPS="WP-001\nWP-002\nWP-003"
for wp in $WPS; do          # zsh: $WPS — один элемент без word-split
  grep -q "$wp" plan.md     # BSD grep: если $wp содержит \n — матч по любой строке
done
```

Итог: цикл выполнился 1 раз для всей строки `$WPS`, grep нашёл что-то (из-за многострочного паттерна) → false-green несколько дней подряд.

## Диагностика

- `echo "$WPS" | wc -w` → если 1 при ожидаемых N — word-split не работает
- `printf '%s\n' $WPS | wc -l` → то же, явный подсчёт
- Тест BSD grep: `echo "one" | grep -q $'two\nthree'` → возвращает 0 (match!) на macOS

## Фикс

```bash
# Вариант 1: while read
while IFS= read -r wp; do
  grep -q "$wp" plan.md
done <<< "$WPS"

# Вариант 2: xargs с null-разделителем
printf '%s\0' $=WPS | xargs -0 -I{} grep -q "{}" plan.md

# Вариант 3: bash с явным word-split (не zsh)
IFS=$'\n' read -r -a wps_arr <<< "$WPS"
for wp in "${wps_arr[@]}"; do ...
```

## Связи

- DP.FM.208 (macOS bash 3.2 IFS-tab) — тот же класс «тихий провал из-за shell-portability», другой механизм
