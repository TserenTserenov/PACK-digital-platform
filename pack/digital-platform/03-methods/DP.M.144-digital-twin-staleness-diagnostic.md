---
id: DP.M.144
name: "Digital Twin staleness diagnostic — calc_at before code"
name_ru: "Диагностика устаревания Цифрового двойника — calc_at до кода"
type: method
status: draft
created: 2026-05-22
trust:
  F: 3
  G: domain
  R: 0.9
epistemic_stage: established
related:
  see_also: [DP.D.052]
  references: ["HD #27", FORM.089]
tags: [digital-twin, troubleshooting, derived-aggregate, cache-invalidation]
wp: WP-327 Ф5c (commit 0b5eac2e)
---

# DP.M.144 — Диагностика жалоб «неправильная ступень/прогресс/баллы» — calc_at до кода

## 1. Проблема

Пилот жалуется: «бот показывает не ту ступень / прогресс / квалификацию / бонусы». Первый импульс инженера — лезть в код UI или в логику расчёта функций. В 80%+ случаев причина не там, а в **staleness** агрегата `digital_twins.data`: коллекторы первичных сигналов обновили данные, но cron-пересчёт ЦД ещё не пробежал.

## 2. Алгоритм

```
1. SQL → проверить calc_at
2. Если stale → пересинхронизировать
3. Если actual + неверно → лезть в код
```

### Шаг 1 — SELECT calc_at

```sql
SELECT
  user_id,
  calc_at,
  data->'3_derived'->'3_4_qualification' AS qualification,
  data->'3_derived'->'3_5_indicators' AS indicators
FROM digital_twins
WHERE user_id = '<UUID>';
```

Если `calc_at` старше суток (для daily-cron) или старше часа (для hourly-cron) → переходим к Шагу 2.

### Шаг 2 — Pересинхронизация

Вариант A (через бот): `/dt_sync` (или соответствующая admin-команда).
Вариант B (вручную): прямой вызов `calc_student_stage(user_id)` / `recalculate_derived(user_id)`.

После пересинхронизации повторить SELECT — проверить, что `calc_at` обновился И значения изменились.

### Шаг 3 — Если actual И неверно

Только теперь идём в код:

- Сначала смотрим **коллекторы первичных сигналов** (`2_collected.2_6_coding`, `2_7_iwe`, `2_8_world` и т.д.) — не нулевые ли, не отстают ли по времени.
- Затем смотрим **функции расчёта derived** (`calc_*`).
- Затем — UI-логику бота.

## 3. Антипаттерн

```
изменил код → запустил → не помогло → ещё код → ещё код → коммит ради коммита
```

При работающем коде и stale-кеше этот цикл бесконечен. Признак, что вы в антипаттерне: **3 коммита подряд без проверки calc_at**.

## 4. Применимо

Ко всем computed-полям ЦД:
- ступень обучения (Аттестатор)
- бонусы (DP.D.050)
- баллы (earned_total)
- прогресс саморазвития
- любой Derived-агрегат FORM.089

## 5. Тест применимости

«Это поле Derived (вычисляется агрегатно) или Observed (первичный сигнал)?»
- Derived → начни с calc_at
- Observed → начни с коллектора

## 6. Связи

- HD #27, DP.D.052 (Persona / Memory.Observed / Memory.Derived / Context).
- FORM.089 §6.1 — карта Derived-полей.
