---
id: DP.WP.005
name: WeekReport
type: work-product
status: active
summary: "Итоги недели (WeekReport) — отдельный документ недельного отчёта. *Примечание: в марте 2026 указан deprecated как prerejection для ОПТ-5, но впоследствии (РП297) формат восстановлен — используется как расчётный тайл панели F5."
created: 2026-02-25
trust:
  F: 2
  G: domain
  R: 0.5
epistemic_stage: emerging
related:
  produced_by: [S03]
  consumed_by: []
  services: [S03]
---

# WeekReport

> **История статуса:**
> - 2026-03-25 — объявлен deprecated (prerejection ОПТ-5), предполагалось слияние в WeekPlan.
> - 2026-05-20 — статус восстановлен (РП297): WeekReport используется как расчётный тайл панели F5; формат сохранён.
> - Текущий статус: active, используется S03.

## Куда перенесено

| Было (WeekReport) | Стало (WeekPlan § Итоги) |
|-------------------|--------------------------|
| Статистика по репо | WeekPlan → «Итоги W{N-1}» → таблица по репо |
| Закрытые РП | WeekPlan → «Итоги W{N-1}» → completion rate |
| Ключевые достижения | WeekPlan → «Итоги W{N-1}» → инсайты |
| Carry-over | WeekPlan → «Итоги W{N-1}» → carry-over |
| Пост итогов | Создаётся S03 напрямую (DS-Knowledge-Index) |

## Связанные документы

- [DP.WP.004 WeekPlan](DP.WP.004-week-plan.md) — единый документ недели (план + итоги)
- [DP.MAP.002 S03](../07-map/DP.MAP.002-iwe-service-catalog.md) — сервис Week Review (обновляет секцию в WeekPlan)
