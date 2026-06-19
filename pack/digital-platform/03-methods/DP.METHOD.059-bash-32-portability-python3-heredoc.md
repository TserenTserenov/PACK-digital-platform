---
id: DP.METHOD.059
title: "bash 3.2 portability: python3 heredoc для declare -a с пробелами в элементах"
type: method
pack: DP
tags: [bash, portability, macos, bash-3.2, declare-a, python3, heredoc, cli]
status: draft
valid_from: 2026-06-16
schema_version: 1
---

# DP.METHOD.059 — bash 3.2 portability: python3 heredoc для declare -a с пробелами

## Описание

macOS поставляется с bash 3.2 (GPLv2, Apple не обновляет). `declare -a arr=("word one" "word two")` в bash 3.2 не поддерживает элементы с пробелами через env-экспорт. Паттерн: передавать значения как отдельные env-переменные `SKILL_PHRASES_0`, `SKILL_PHRASES_1` и генерировать bash-код через python3 heredoc.

## Проблема

```bash
# Ломается в bash 3.2 при элементах с пробелами:
export ITEMS="hello world"
declare -a ARR=($ITEMS)   # → ("hello" "world") — разбит по пробелу
```

## Решение (IPO)

**Вход:** env-переменные `SKILL_PHRASES_0`, `SKILL_PHRASES_1`, ... (по одной на элемент)

**Процесс:** python3 heredoc генерирует bash-код:
```python
import os
phrases = []
i = 0
while f"SKILL_PHRASES_{i}" in os.environ:
    phrases.append(os.environ[f"SKILL_PHRASES_{i}"])
    i += 1
items = " ".join(f'"{p}"' for p in phrases)
print(f'declare -a PHRASES=({items})')
```

**Выход:** `declare -a PHRASES=("hello world" "foo bar")` → eval в скрипте

## Применение

CLI-инструменты IWE, которые должны работать на macOS out-of-the-box без homebrew bash. Один скрипт работает на bash 3.2 (macOS) и bash 4+ (Linux).

## Тест применимости

«Скрипт запускается на macOS `/bin/bash --version` = 3.2 без ошибок с multi-word элементами?» Да → паттерн реализован корректно.

## Источник

session-transcript 2026-06-16; git commit 9f8bf01 (scaffold-init.sh WP-422 Ф3)
