---
id: DP.METHOD.064
type: method
domain: DP
status: draft
summary: "gate:outcome-pending — formal interim phase status between 'mechanism verified' and 'prod behaviour confirmed'. Prevents premature phase closure when tests pass but production observation period not yet complete."
created: 2026-06-24
valid_from: 2026-06-24
version: v1.0
source: "session-transcript 2026-06-24 peer-02 WP-149; WP-149.md outcome_gate"
related:
  see_also: [DP.METHOD.065]
  complements: "distinctions.md: Приёмка обещания ≠ Прод-наблюдение"
tags: [wp-lifecycle, acceptance, phase-status, outcome-gate, time-bounded]
---

# DP.METHOD.064: gate:outcome-pending

## Назначение

Формальный промежуточный статус фазы рабочего продукта между «механика верифицирована» (тесты/чеклист OK) и «поведение подтверждено в проде за период T». Предотвращает преждевременное закрытие фазы, когда функциональная верификация пройдена, но time-bounded acceptance criterion ещё не истёк.

## Применять когда

- Фаза имеет time-bounded acceptance: «N дней без регрессий», «K пользователей без жалоб»
- Между тестами и закрытием нужно явное ожидание на production behaviour
- Нужно отличить «тесты прошли» от «поведение в проде подтверждено»

## Паттерн (frontmatter WP-карточки)

```yaml
phases:
  - id: Ф-rung-fidelity
    status: gate:outcome-pending
    outcome_gate:
      deadline: 2026-07-01
      criterion: "7 дней без регрессий B-серии"
      verified_at: null          # заполняется при закрытии
```

## Переходы

```
in_progress → [тесты PASS + чеклист OK] → gate:outcome-pending
gate:outcome-pending → [deadline без регрессий] → done
gate:outcome-pending → [регрессия обнаружена] → in_progress (сброс deadline)
```

## Связи

- Дополняет: [[Приёмка обещания ≠ Прод-наблюдение]] — добавляет промежуточный статус
- Совместно: DP.METHOD.065 (verifier-before-assembly) для pipeline-фаз
