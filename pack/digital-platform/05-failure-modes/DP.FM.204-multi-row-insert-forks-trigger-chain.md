---
id: DP.FM.204
title: "Multi-row INSERT форкает trigger-based append-only цепочку: AFTER-триггер ранней строки не виден BEFORE-триггеру поздней в том же стейтменте"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / postgresql / trigger-visibility
epistemic_stage: confirmed
valid_from: 2026-07-06
source: "session-close 2026-07-03 (WP-457 Ф12, sessions/2026-07/2026-07-03-17-wp455-critical-review-and-urgent-deploy.md §2)"
related:
  see_also: [DP.FM.203, DP.METHOD.101]
---

# DP.FM.204 — Multi-row INSERT форкает trigger-based append-only цепочку

## Описание

Trigger-based immutable chain реализована через BEFORE-триггер, читающий tip-таблицу (указатель на последнюю запись). При `INSERT...VALUES` с несколькими строками в одном стейтменте: AFTER-триггер обновляет tip после первой строки, но BEFORE-триггер второй строки видит tip до этого обновления — то же значение, что видела первая строка. Обе строки получают одинаковый `prev_hash` → цепочка форкается.

## Пример

WP-457 Ф12: бэкфилл через `INSERT INTO table VALUES (row1), (row2), ..., (row8)`. BEFORE-триггер row2 читал tip в момент до того, как AFTER-триггер row1 обновил его. Результат: все 8 строк получили одинаковый `prev_hash`. Обнаружено при живой проверке данных после деплоя.

## Тест обнаружения

«API или скрипты позволяют batch-INSERT в таблицу с trigger-based chain?» Да → FM.204 применим. Проверка: `SELECT prev_hash, count(*) FROM table GROUP BY prev_hash HAVING count(*) > 1`.

## Инвариант

PostgreSQL BEFORE-триггер в multi-row INSERT видит состояние таблицы до выполнения любого AFTER-триггера из того же стейтменте. Tip-паттерн корректен только для single-row INSERT.

## Митигация

1. Запретить batch INSERT в документации API таблицы с trigger-based chain (README + schema comment)
2. Добавить assertion после каждой batch-операции: `SELECT check_no_forks()`
3. Если batch нужен: перейти на loop по одной строке в транзакции, или использовать отдельный механизм (deferred trigger + explicit commit per row)

## Связи

- DP.FM.203 (Задеплоенный консенсус ≠ финал проверки) — FM.204 — конкретная причина того, что FM.203 произошёл
- DP.METHOD.101 (Append-only audit journal integrity) — архитектурный контракт, нарушаемый этим FM
