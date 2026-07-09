---
id: DP.METHOD.134
type: method
pack: PACK-digital-platform
domain: digital-platform / template-delivery
trust: draft
epistemic_stage: empirical
valid_from: 2026-07-06
source: "session-close 2026-07-06 + git diff FMT-exocortex-template commit 60e3591 (fix #226)"
schema_version: 1
related:
  see_also: [DP.M.009]
---

# DP.METHOD.134 — Отложенный выход при конфликте authored-файла в template-sync доставке

## Описание

В скриптах обновления шаблона (template-sync) authored-файлы (намеренно расходящиеся с платформой) могут конфликтовать при merge. Паттерн: конфликт authored-файла НЕ прерывает доставку — выход откладывается до завершения propagation всех остальных компонентов (memory/, hooks/, skills/).

## Алгоритм

1. Attempt merge authored-файла (CLAUDE.md и подобных)
2. При конфликте — записать в внутренний conflict-log (не выходить с ошибкой)
3. Продолжить propagation остальных компонентов — независимо от конфликта
4. После завершения всей доставки — surface конфликты и выйти с deferred exit code (e.g., exit 49)

## Инвариант

Authored-файлы — намеренное расхождение пользователя с шаблоном. Конфликт в authored-файле = ожидаемое состояние, не ошибка. Abort-on-authored-conflict = потеря всех последующих обновлений (memory, hooks, skills) ради одного ожидаемого конфликта.

## Тест применения

«Конфликт в authored-файле возник — были ли доставлены memory/hooks/skills?»
- Нет → abort-on-conflict (антипаттерн)
- Да → deferred exit (правильно)

## Антипаттерн

Abort при первом authored-конфликте: все компоненты после конфликтующего файла не доставляются. Пользователь получает частичное обновление без полного сигнала о причине.

## Связи

- `DP.M.009` — Расширяемость шаблонных систем (authored zone, overlay pattern)
