---
id: DP.M.114
type: method
name: "Исторический cap бонусов: интеграл по истории квалификации"
domain: digital-platform
status: draft
trust: medium
epistemic_stage: instance
source: "DS-my-strategy commit 74429eab, WP-327 формула бонусов из applied_events.qualification_snapshot"
valid_from: 2026-05-20
---

# Исторический cap бонусов: интеграл по истории квалификации

## Суть

Когда cap зависит от level/tier, изменяющегося со временем, — cap должен быть интегралом
по истории (Σ), а не произведением текущего level на total_days.

## Формула

```
bonus_balance = min(earned_total, Σ(active_days_at_qual_i × daily_cap_i))
```

Где `active_days_at_qual_i` — количество активных дней, когда пилот имел квалификацию i.
Данные берутся из `applied_events.qualification_snapshot`.

## IPO

**Вход:** applied_events с qualification_snapshot (снимок квалификации на момент события)
**Процесс:** для каждого tier i → count(active_days at qual i) × daily_cap(i) → сумма
**Выход:** historical_bonus_cap (максимальные бонусы, которые пилот мог накопить)

## Мотивация

Без исторического cap: пилот, повысивший квалификацию вчера,
получает высокий cap за все исторические дни → несправедливо (retroactive windfall).
С историческим cap: cap = то, что пилот реально мог бы накопить при его квалификации в каждый день.

## Применение

Subscription-based или tier-based reward системы с прогрессией уровней пользователей,
где daily_cap зависит от текущего tier.

## Связи

- Реализация: WP-327, applied_events.qualification_snapshot
- Различение: DP.D.050 (Баллы ≠ Бонусы ≠ Ступень)
- Предыдущий метод: DP.M.113 (разделение earned_total и points)
