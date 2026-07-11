---
name: Parallel agent branch switch in shared checkout
id: DP.FM.258
domains: [multi-agent-coordination, git-workflows]
tags: [concurrency, git-state, silent-bug]
severity: high
---

# DP.FM.258 — Параллельный агент переключает HEAD в общем рабочем дереве

## Суть

Два агента работают в одном дереве. Агент B выполняет `git checkout -b peer/...` между шагами Агента A. Это изменяет глобальный HEAD. Если Агент A вслед за этим выполнит `git commit -am "..."`, коммит создастся на ветке B, а не на A. Обнаруживается только явной проверкой `git branch --show-current` после коммита.

## Инцидент

WP-474 (2026-07-10): `git checkout -b peer/wf-branch` из другой сессии сдвинул HEAD. Коммит попал на `peer/wf-branch` вместо `main`. В отчёте утверждал запуск на main — фактически не был.

## Fix

- Проверить текущую ветку перед commit: `git rev-parse --abbrev-ref HEAD`
- После commit: снова проверить ветку перед push
- Вместо checkout+commit использовать: `git push origin <local>:<remote>`
- Или настроить pre-commit хук, блокирующий коммит если текущая ветка != конфигурированная

## Паттерн

В многоагентном окружении — никогда не доверять `HEAD` между шагами. Всегда явно проверять.
