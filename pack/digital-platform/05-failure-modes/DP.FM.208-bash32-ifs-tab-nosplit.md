---
id: DP.FM.208
type: failure-mode
title: "macOS bash 3.2 не производит word-split по \\t как IFS — read_yaml() возвращает пустые значения"
pack: PACK-digital-platform
domain: platform-tooling
status: draft
valid_from: 2026-07-04
source: "session-close 2026-07-04 (WP-7, DS-my-strategy 8682b8aa4 + FMT 1256027)"
schema_version: 1
related:
  see_also: [DP.FM.033, DP.METHOD.059]
---

# DP.FM.208: macOS bash 3.2 IFS tab split failure

## Описание

На macOS system bash (версия 3.2.x, предустановленная в macOS) `IFS=$'\t'` не производит word-split при чтении через `read`. Скрипты, использующие tab как разделитель ключ/значение (например, `read_yaml()`), всегда возвращают пустые строки. На GNU bash 4.x+ тот же код работает корректно.

## Класс дефекта

Тихий провал: функция не выдаёт ошибку, возвращает пустое значение без предупреждения.

## Ситуация возникновения

- `IFS=$'\t' read -r key val <<< "name\tvalue"` — val пустая
- `read key val < file` с `IFS` содержащим только `\t`
- Любой `read` с tab-only IFS под macOS system bash

## Фикс

Использовать `\x1f` (ASCII 31, Unit Separator) или `\x01` вместо tab. Не встречается в нормальном тексте YAML/конфигурации, работает стабильно в bash 3.2 и 5.x.

```bash
# Было (сломано на macOS bash 3.2):
IFS=$'\t' read -r key val <<< "${line}"

# Стало (работает везде):
IFS=$'\x1f' read -r key val <<< "${line}"
# При формировании строки: key$'\x1f'value
```

## Диагностика

`bash --version` → если < 4.0 → tab как IFS ненадёжен. Тест: `echo "a	b" | IFS=$'\t' read a b; echo "[$a][$b]"` — `[][]` = баг.

## Обобщение

Любой непечатный ASCII 1-31 безопаснее пробельных символов как IFS-разделитель при работе с macOS system bash.

## Связи

DP.METHOD.059 (bash 3.2 portability: python3 heredoc для declare -a с пробелами) — тот же класс ограничений macOS system bash 3.2, другой конкретный симптом (word-split в declare -a vs read).
