---
id: DP.WP.005
name: WeekReport
type: work-product
status: deprecated
summary: "DEPRECATED: итоги недели теперь записываются в секцию «Итоги W{N-1}» внутри WeekPlan (DP.WP.004). Отдельный файл WeekReport больше не создаётся."
created: 2026-02-25
deprecated: 2026-03-25
trust:
  F: 2
  G: domain
  R: 0.5
epistemic_stage: emerging
related:
  replaced_by: [DP.WP.004]
  produced_by: [S03]
  consumed_by: []
  services: [S03]
---

# WeekReport (DEPRECATED)

> **Решение от 2026-03-25:** WeekReport как отдельный файл упразднён.
> Итоги недели записываются в секцию «Итоги W{N-1}» внутри **WeekPlan W{N}** (DP.WP.004).
> Причина: дублирование данных — секция итогов в WeekPlan содержала те же данные, что и WeekReport.

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
