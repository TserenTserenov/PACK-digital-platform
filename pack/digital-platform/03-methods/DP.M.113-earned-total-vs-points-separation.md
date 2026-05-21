---
id: DP.M.113
type: method
name: "Разделение earned_total и points в gamification схеме"
domain: digital-platform
status: draft
trust: medium
epistemic_stage: instance
source: "DS-my-strategy commit 74429eab + 38a3a8bb, WP-327 формула бонусов"
valid_from: 2026-05-20
---

# Разделение earned_total и points в gamification схеме

## Суть

В reward-системе с тратами нельзя хранить «достижение» и «текущий баланс» в одном поле:
при трате теряется информация о суммарных заработанных баллах → лидерборд становится несправедливым.

## IPO

**Вход:** grant-события (reward), spend-события (покупки)
**Процесс:**
- `earned_total += amount` при каждом grant (НИКОГДА не убывает)
- `points = earned_total − spent` (текущий баланс)
- `bonus_balance` — вычисляется из applied_events, не хранится

**Выход:** earned_total для лидерборда; points для трат; bonus_balance для скидок

## Правила

1. earned_total — монотонный счётчик, только grant-события
2. points = earned_total − spent (текущий баланс для трат)
3. bonus_balance = `min(earned_total, Σ(active_days_at_qual_i × daily_cap_i))` — вычисляется
4. Лидерборд строится на earned_total, не на points

## Failure mode

Хранить только points (earned − spent): щедро тратящие пилоты получают низкий рейтинг
при высокой активности → несправедливый лидерборд.

## Связи

- Реализация: WP-327, таблица point_balances (column earned_total — TODO Этап 4)
- Различение: DP.D.050 (Баллы ≠ Бонусы ≠ Ступень)
- Следующий метод: DP.M.114 (исторический cap)
