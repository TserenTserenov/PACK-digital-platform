---
id: DP.M.195
name: Pull-driven feature activation — defer до explicit user request
status: active
trust: experimental
epistemic_stage: emerging
created: 2026-05-27
related:
  - DP.M.158  # archgate-defer-pattern
  - DP.M.163  # deferred-phase-finalization-checkpoint
s2r_families: [F5]
---

# DP.M.195: Pull-driven feature activation

## Суть

Паттерн фильтрации backlog для green-field / single-user систем: фича остаётся в **defer-статусе** до **explicit user request на конкретный subset**. Триггер — pull, не push. До явного запроса работа не начинается.

## Анти-триггеры (over-engineering сигналы)

- «Threshold по count» — реализуем «когда юзеров будет N». Чаще всего N не наступает или наступает позже, чем нужно.
- «When-needed» — нет owner'а решения, проявляется как WIP без throughput.
- «Просто сделать» — sunk cost от любой реализации без pull-сигнала.

## Правильный триггер

**Explicit user request на конкретный subset.** Формализуется как TTL-event в backlog:

> Backlog item X — заблокирован «до первого explicit request на capability Y».

При срабатывании триггера:
1. Записать кто, когда, что именно попросил (свидетельство pull-сигнала).
2. Только тогда начинать реализацию subset'а, на который был запрос.
3. Не расширять scope превентивно («раз делаем — давай ещё»).

## Применимость

- Capability shortcuts (keyboard, CLI).
- UI affordances (advanced filters, custom themes).
- Optional tooling.
- Дополнительные интеграции.
- Шаблоны / boilerplate.

Везде, где «было бы удобно» ≠ «реально нужно сейчас».

## Границы

- **Не применять** к дефолтным pipeline-кирпичам — там pull всегда есть (без них продукт не работает).
- **Не применять** к security/compliance-фичам — pull появляется только после инцидента, слишком поздно.

## Anti-паттерн

«Если просто сделать — давай сделаем.» = WIP/inventory без throughput. Сделанная фича без потребителя становится поддерживаемым артефактом (доки, тесты, регрессии) без отдачи.

## Inventory как побочный эффект

Inventory не растёт скрыто, потому что:
- Невыполненная работа явно зависит от внешнего сигнала.
- Backlog item имеет видимое условие активации (TTL-event).
- При обзоре backlog'а легко отделить «pulled» от «pushed» элементов.

## Источник

session-transcript 2026-05-27 (peer-сессия 22 wp358-capability-shortcuts, Темы 1+4).
