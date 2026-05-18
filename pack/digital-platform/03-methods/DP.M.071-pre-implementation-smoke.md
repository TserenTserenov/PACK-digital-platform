---
id: DP.M.071
name: Pre-implementation smoke
kind: Method
status: active
sources:
  - WP-295 Ф1 шаг 1 (commit a4c13150, 2026-05-17)
  - session-close-feed 2026-05-17
domain: PACK-digital-platform
tags: [validation, schema, pre-impl, smoke, sql-draft, walkthrough, contract-vs-schema]
---

# DP.M.066: Pre-implementation smoke

## Определение

**Pre-implementation smoke** — метод дешёвой валидации схемы/контракта ДО миграции БД или развёртывания: SQL-draft целевой схемы в sandbox-файле + ручной walkthrough одной fake-сессии через все таблицы и поля + фиксация выявленных issues. Стоимость ~1.5h.

Применяется как **Ф1 шаг 1 gate** между Ф0 (Подготовительная — Pack-формализация SC/ROLE/SOTA) и Ф1+ (миграция БД, код).

## Алгоритм

1. **SQL-draft в sandbox.** Написать CREATE TABLE / схему ВСЕХ таблиц целевой реализации в одном sandbox-файле (не в production migrations). Без индексов, без триггеров — только структура полей и FK.
2. **Walkthrough одной fake-сессии.** Пройти один realistic-кейс (для agent_trace — одну сессию агента «start → 2 decisions → 1 fork → end») через все таблицы: какие INSERT'ы происходят, какие UPDATE'ы, какие SELECT'ы делает downstream-роль.
3. **Зафиксировать issues.** Каждое расхождение (missing field, contract-vs-schema mismatch, инвариантное противоречие, semantic gap) — в WP-context как pre-impl-issue.
4. **Закрыть issues ДО миграции.** Каждый pre-impl-issue → fix в Pack (SC update) или в schema (sandbox edit) или в WP-плане (rescope). Только после `issues=∅` — Ф1 шаг 2 (production migration).

## Тест применимости

«Есть ли схема/контракт, которая будет реализована в коде следующей фазой?» Да → pre-implementation smoke перед Ф{first-impl}. Нет (фаза только Pack-формализация без impl) → не нужно.

## Прецеденты

- **WP-295 Ф1 шаг 1 (2026-05-17):** SQL-draft 7 таблиц agent_trace + walkthrough одной fake-сессии → 8 issues + 1 contract bug (SC.037 инв.2: session как card, не event). Fix-commit `65b29b4` (Pack-обновление инварианта) до Ф1 шаг 2 (миграция). Стоимость ~1.5h.

## Различение

- **Smoke в production (smoke-test после deploy)** ловит infrastructure-issues; **pre-implementation smoke** ловит spec-vs-schema issues ДО deploy. Разные природы.
- **feedback_archgate_independent_review.md (independent review subagent):** review ПОСЛЕ архитектуры (когда план готов); **pre-implementation smoke:** ПОСЛЕ архитектуры, ДО реализации (когда план готов, миграция ещё не сделана). Комплементарны: review проверяет логику плана, smoke проверяет совместимость плана с realistic-кейсом.

## Связано

- [[DP.M.060]] Atomic VDV-step — pre-implementation smoke = atomic-шаг внутри IntegrationGate фазы Ф1
- DP.SC.037 (agent_trace) — first user of method
- feedback_archgate_independent_review.md — комплементарный gate

