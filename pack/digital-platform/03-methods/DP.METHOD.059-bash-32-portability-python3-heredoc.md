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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Совместимость с bash 3.2 macOS ↔ простота одного скрипта на две платформы | Паттерн (env-переменные `SKILL_PHRASES_N` + python3 heredoc-генерация) избегает возможностей bash 4+, чтобы работать «из коробки» на macOS без homebrew, но платит сложностью — лишний процесс python3 и generate-then-eval вместо прямого `declare -a` |
| Отказ от homebrew bash (снижение barrier to entry) ↔ доступ к нативному прямому `declare -a` | Решение сознательно не требует от пользователя ставить обновлённый bash, но отказывается от более простого и читаемого прямого синтаксиса, который был бы доступен, будь bash 4+ гарантирован на целевой машине |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `draft`: пометка `tentative`._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Диагностика не доходит до версии bash | При отладке скрипта, падающего на macOS, внимание практикующего идёт к «версии python» или «правам на файл», а не к версии bash — раздел «Проблема» показывает, что первопричина тихая (bash 3.2 просто молча разбивает строку по пробелу, не бросает ошибку), из-за чего диагностика систематически не доходит до правильного слоя |
| _(tentative)_ Копипаста паттерна без повторной проверки теста | Написав рабочий heredoc-паттерн один раз, практикующий переносит его в новый скрипт копипастой без повторной прогонки «Теста применимости» — внимание смещается с «работает ли конкретно этот случай» на «формат когда-то уже работал», и edge-case с новым набором элементов (пустая строка, спецсимволы) остаётся непроверенным |

## Источник

session-transcript 2026-06-16; git commit 9f8bf01 (scaffold-init.sh WP-422 Ф3)

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
