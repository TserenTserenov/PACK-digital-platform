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

# DP.M.071: Pre-implementation smoke

## Определение

**Pre-implementation smoke** — метод дешёвой валидации схемы/контракта ДО миграции БД или развёртывания: SQL-draft целевой схемы в sandbox-файле + ручной walkthrough одной fake-сессии через все таблицы и поля + фиксация выявленных issues. Стоимость ~1.5h.

Применяется как **Ф1 шаг 1 gate** между Ф0 (Подготовительная — Pack-формализация SC/ROLE/SOTA) и Ф1+ (миграция БД, код).

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Дешевизна smoke (~1.5ч, один sandbox-файл) ↔ репрезентативность одного fake-кейса | Метод намеренно ограничивается ОДНОЙ realistic-сессией (start → 2 decisions → 1 fork → end) ради бюджета ~1.5ч, но edge-cases, не покрытые этим единственным walkthrough-сценарием, останутся невидимыми до реальной миграции — прецедент WP-295 нашёл 8 issues на одном кейсе, что не гарантирует отсутствия issues на других |
| Гейт между Ф0 и Ф1+ (issues=∅ обязательно) ↔ скорость перехода к production migration | Блокирование шага 2 (production migration) до полного закрытия pre-impl-issues защищает от contract-vs-schema mismatch (как SC.037 инв.2 в прецеденте), но каждый найденный issue — это доп. цикл fix в Pack/schema/WP-плане ДО того, как можно начать реализацию, которую команда уже готова писать |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Happy path перевешивает fork-точки | При прохождении единственной fake-сессии (шаг 2: start → 2 decisions → 1 fork → end) внимание тянется к линейному прогону через все таблицы, недооценивая именно fork-точку, где реализация должна разойтись — а расхождения в контракте (как session-как-card vs session-как-event в прецеденте) чаще прячутся не на прямом пути, а в точке ветвления |
| Структурная полнота SQL-draft принимается за смысловую корректность | После того как sandbox-схема покрывает все таблицы и FK (шаг 1), внимание смещается к следующему шагу до того, как реально проверено, соответствуют ли эти поля контракту SC — «схема нарисована» подменяет «схема провалидирована через шаг 2 walkthrough» |

## Связано

- [[DP.M.060]] Atomic VDV-step — pre-implementation smoke = atomic-шаг внутри IntegrationGate фазы Ф1
- DP.SC.037 (agent_trace) — first user of method
- feedback_archgate_independent_review.md — комплементарный gate

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.

