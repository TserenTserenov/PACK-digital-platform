---
id: DP.M.165
name: "Soft streak reset — плавное снижение вместо обнуления"
type: method
domain: digital-platform
status: active
trust: confirmed
epistemic_stage: validated
valid_from: 2026-05-23
source: WP-327 (commit 08b4e3cc, points-model v3)
schema_version: 1
---

# DP.M.165 — Soft streak reset: плавное снижение streak вместо обнуления

## Описание

При пропуске дня streak снижается плавно (на фиксированное N или коэффициент ×0.9), а не обнуляется до нуля.

## Проблема, которую решает

**Hard reset** (обнуление при одном пропуске) создаёт «обвалы мотивации»:
пользователь потерял 30-дневный streak → нет смысла продолжать → отток.

Streak = нелинейный мотиватор: потеря 30→0 психологически = смерть прогресса.

## Алгоритм (вариант A — линейное снижение)

```python
def update_streak_on_miss(current_streak: int, miss_days: int, decay_per_day: int = 5) -> int:
    """
    Снижает streak на decay_per_day за каждый пропущенный день.
    Минимум = 1 (не обнуляется полностью, если streak > 0).
    """
    new_streak = current_streak - (decay_per_day * miss_days)
    return max(1, new_streak)
```

## Алгоритм (вариант B — экспоненциальное снижение)

```python
def update_streak_on_miss(current_streak: int, miss_days: int, decay_factor: float = 0.9) -> int:
    new_streak = current_streak * (decay_factor ** miss_days)
    return max(1, int(new_streak))
```

## Применение

- Learning platforms (Duolingo, фитнес-приложения, habit trackers)
- Любая система с streak-механикой, где разрыв цепочки не должен означать потерю всего прогресса

## Компромисс

Soft reset **снижает «ставку»** на streak → меньше мотивация поддерживать непрерывность.
Калибровать через A/B: сравнить retention D30 hard vs soft reset.

## Порог срабатывания

Рассматривать soft reset при условии: ≥15% пользователей теряют streak ≥7 дней → измеримый отток после обнуления.

## Связи

- WP-327 (points-model-design v3, 23 мая 2026)
- DP.M.166 (referral credit vs points)
