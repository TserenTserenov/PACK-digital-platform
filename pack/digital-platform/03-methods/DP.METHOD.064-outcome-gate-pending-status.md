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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Строгость time-bounded критерия (`criterion: "7 дней без регрессий"`) ↔ гибкость реакции на регрессию | Жёсткий deadline даёт понятный триггер `gate:outcome-pending → done`, но при обнаружении регрессии весь период ожидания обнуляется (переход в `in_progress`, сброс deadline) — справедливо для проверяемой фазы, но резко для регрессии, найденной в смежном месте |
| Явность промежуточного статуса `gate:outcome-pending` ↔ простота бинарного done/not-done для внешнего наблюдателя | Третье состояние точнее отражает разницу «механика верифицирована» / «поведение подтверждено», но усложняет чтение статуса фазы теми, кто ожидает только два значения |
| Формальность `verified_at: null` до закрытия ↔ соблазн заполнить поле раньше срока | `null` явно маркирует «ещё не подтверждено», но соседнее поле `deadline` создаёт искушение проставить `verified_at` по факту наступления даты, не проверив реальное отсутствие регрессий за весь период |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `draft`: пометка `tentative` по прецеденту WP-448 Ф12._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Проверка критерия подменяется проверкой даты | При наступлении `deadline` внимание тянется закрыть фазу по факту даты, а не по факту критерия («7 дней без регрессий») — дата в календаре заметнее и проверяется одним взглядом, тогда как непрерывное отсутствие регрессий за весь период требует ретроспективного просмотра |
| _(tentative)_ Сброс deadline теряется на фоне самой регрессии | При переходе `gate:outcome-pending → in_progress` из-за найденной регрессии внимание концентрируется на самой регрессии (что чинить) и недооценивает сброс deadline как отдельный явный шаг — переход фиксируется, а обновление даты нового периода наблюдения откладывается |

## Связи

- Дополняет: [[Приёмка обещания ≠ Прод-наблюдение]] — добавляет промежуточный статус
- Совместно: DP.METHOD.065 (verifier-before-assembly) для pipeline-фаз

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
