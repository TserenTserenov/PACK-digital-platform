---
id: DP.M.066
name: Multi-round verifier с сужающимся scope
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-326 verifier rounds (commits 68cbfb4, 07ebd74, 7e3c5e6, 8a969fc, 53cff04)
related:
  complements: [DP.M.012, DP.M.060]
  applies_to: [VR.R.001]
---

# DP.M.066: Multi-round verifier с сужающимся scope

## Определение

Verifier-агент (например, VR.R.001) при проверке spec↔impl alignment не достигает полноты за один проход. Метод: последовательность из N round'ов, каждый со **строго меньшим scope-prompt'ом** предыдущего.

**Типичная последовательность для двухслойных РП (Pack + impl):**

1. **Round 1 — broad:** код-стиль, обещания, общее выравнивание с Pack.
2. **Round 2 — constants / naming:** мёртвые импорты, литералы, timer-значения, имена-константы.
3. **Round 3 — pinned section:** одна секция одного Pack-файла, alignment pseudocode с новой моделью.

## Stop-criterion

> Round-N не находит ничего → verifier завершён.

Если последний round нашёл хотя бы одну ошибку — нужен ещё один round (более узкий scope).

## Зачем

Широкий scope перекрывает узкие ошибки своими: при round 1 «общего обзора» не видны конкретные мёртвые импорты, при round 2 «констант» не видна неактуальность pseudocode. Каждый последующий round открывает невидимый слой.

## Когда применять

- Закрытие РП, изменяющего ≥2 layer'а одновременно (Pack + impl, Pack + downstream-роль, контракт + код).
- Любые spec↔impl аудиты, где «один pass» исторически давал residual drift.

## Когда **не** применять

- Тривиальные изменения (1 коммит, 1 файл) — overhead.
- Творческие документы без spec-обязательств — round 1 = ревью человеком.

## Различение с manual review

Manual review = одна попытка одним проверяющим. Multi-round verifier = последовательность agent-проходов, каждый с явно изменённым scope-prompt'ом (broad → constants → naming → Pack-section). Различие не в количестве проходов, а в **сужении scope с каждым проходом**.

## Связь с другими методами

- [[DP.M.060]] (атомарные шаги) — даёт нижнюю границу декомпозиции, чтобы post-condition был формулируем; этот метод — как **проверять** уже атомарные шаги через несколько проходов.
- [[DP.M.012]] Machine-Check Postcondition — машинная проверка одного шага; multi-round verifier — оркестрация N таких проверок над одним deliverable.
- Расширяет контракт VR.R.001 в PACK-autonomous-agents.

## Источник

WP-326 закрытие — 5 verifier-коммитов с явным сужением scope: round 1 (broad alignment), round 2 (dead imports + timer drift), round 3 (FORM.089 §6.1 pseudocode под новую модель). Прецедент зафиксирован 2026-05-17.
