---
id: DP.M.183
title: "Уровнезависимые лимиты бонусов с EMA-сглаживанием"
type: method
domain: digital-platform
source: "git diff DS-ecosystem-development, docs(WP-327): v4.2 final, 2026-05-27"
trust: 0.8
epistemic_stage: operational
valid_from: 2026-05-27
---

# DP.M.183 Уровнезависимые лимиты бонусов с EMA-сглаживанием

## Описание

Бонусный лимит за день (`daily_cap_i`) зависит от текущей ступени (квалификации) пользователя. При смене ступени лимит не меняется мгновенно — EMA с коэффициентом α=0.05 обеспечивает плавный переход.

## Инварианты

1. Чем выше ступень → тем выше `daily_cap`
2. `daily_cap_effective = EMA(daily_cap_raw, α=0.05)` — скачки сглаживаются
3. Gamification усиливает мотивацию к развитию, а не только вознаграждает активность

## Формула

```
daily_cap_effective[t] = α × daily_cap_raw[t] + (1 - α) × daily_cap_effective[t-1]
```

Где `daily_cap_raw[t]` определяется ступенью пользователя на дату `t`.

## Когда применять

- Система накопления бонусов с несколькими уровнями пользователей
- Требуется избежать «шоковых» изменений лимитов при изменении уровня
- Gamification должна поощрять рост квалификации (не только активность)

## Связи

- DP.ROLE.051: Points Redeemer (использует `daily_cap` при redemption)
- distinctions.md: Баллы (earned_total) ≠ Бонусы (daily_cap_bounded) ≠ Ступень (cp-профиль)
