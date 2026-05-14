---
id: DP.M.024
name: "Fallback-поле для NULL в темпоральных расчётах с legacy-данными"
type: method
layer: L4-Personal
status: draft
valid_from: 2026-05-11
tags: [data-engineering, legacy, temporal, fallback]
spf_root: U.Method
trust: anecdotal
epistemic_stage: draft
source: WP-303
---

# DP.M.024 — Fallback-поле для NULL в темпоральных расчётах с legacy-данными

## Описание

При расчётах, основанных на времени первого события (дата регистрации, начало активности, первый контакт), legacy-пользователи могут иметь NULL в первичном поле (`first_event_at`), так как поле появилось после их регистрации.

**Правило:** для каждого темпорального расчёта с legacy-данными — явно определить fallback-поле с семантикой «наиболее ранняя доступная дата существования объекта».

## Input → Process → Output

**Input:** SQL-запрос или агрегат с первичным временным полем, которое может быть NULL для части записей

**Process:**
1. Определить первичное поле (например, `first_event_at`)
2. Найти резервное поле с гарантированным NOT NULL (например, `first_seen_at`, `created_at`)
3. Применить `COALESCE(first_event_at, first_seen_at)` в расчёте
4. Документировать: какое поле основное, какое fallback, почему

**Output:** Темпоральный расчёт без NULL-пробелов для legacy-записей

## Применимость

- Streak-расчёты (сколько недель активен пользователь)
- Возраст аккаунта (`age_weeks`, `days_since_join`)
- Когортный анализ (дата вступления в когорту)
- Ступень мастерства (stage calc через min-агрегат)

## Антипаттерн

Использовать только первичное поле без fallback → legacy-записи получают NULL → расчёт даёт некорректный результат или ошибку агрегации.

**Пример инцидента (WP-303, 2026-05-11):** `first_event_at` NULL для старых пользователей → `compute_stage` давал некорректный `age_weeks` → ступень не повышалась. Решение: `COALESCE(first_event_at, first_seen_at)`.

## Связи

- SPF: U.Method (способ действия, IPO)
- DP.ARCH.004 — физическая схема Neon (поля first_event_at, first_seen_at в users/full_users view)
- DP.FM.014 — смежно: Legacy Port Jump (ошибка проектирования при миграции, не путать с данной FM данных)
