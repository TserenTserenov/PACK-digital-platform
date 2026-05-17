---
id: DP.FM.036
name: WakaTime Measurement Scope Bias (Систематическое завышение через трекер без доменного scope)
category: measurement
severity: major
status: draft
summary: "Системный трекер активности (WakaTime, IDE session, GitHub commits) измеряет все репо без фильтра по домену. При использовании как прокси «инвестиций в X» — систематическое завышение в 3-5×."
created: 2026-05-15
valid_from: 2026-05-15
related:
  see_also: [PD.FORM.089]
tags: [measurement, metrics, bias, wakatime, tracking, activity-hub]
source: "WP-310 Ф-Б, PACK-personal commit 1a54520, 15 мая"
schema_version: 1
---

# [DP.FM.036] WakaTime Measurement Scope Bias

## Суть паттерна

Системный трекер активности (WakaTime, GitHub commit time, IDE session time) фиксирует активность во **всех** репозиториях и контекстах, не только в целевом. При использовании как прокси «инвестиций в X» возникает систематическое завышение — часто в 3–5 раз.

## Механизм

1. `coding_time` (WakaTime) = время в редакторе за всё рабочее время дня
2. Рабочий код + личные проекты + хобби + саморазвитие = неразличимы
3. В рабочие дни `coding_time ≈ 8–9ч`, из которых саморазвитие — 0.5–1ч

## Примеры

- `bh.inv` (инвестированные часы в саморазвитие) = `coding_time` → 9ч/день вместо 1ч
- GitHub commit frequency → «продуктивность» не различает контекст работы

## Правило

**Трекер активности нельзя использовать напрямую как прокси «инвестиций в X» без явного scope-фильтра.**

Для измерения инвестированных часов нужен explicit source:
- Декларация пользователя: `slot_logged.hours`
- Факт прохождения контента: `lesson_completed.duration_minutes / 60`
- Контекст-меченые события с `activity_domain` фильтром

## Применимость

WakaTime, GitHub commit time, IDE session time — любой системный трекер активности без доменной метки.

**Где применим failure mode:** индикаторы **инвестированного времени** (саморазвитие, обучение, практика конкретного домена). Использовать WakaTime напрямую → завышение в 3-5×.

**Где failure mode НЕ применим:** индикаторы **учётного времени** (всё осознанное время у клавиатуры, без разделения на работу/саморазвитие). Для них WakaTime — корректный источник. Пример: мультипликатор IWE [PD.FORM.104](../../../../../PACK-personal/pack/personal-development/02-domain-entities/formalizations/PD.FORM.104-iwe-multiplier.md) использует `coding_time` в знаменателе по назначению — он именно меряет AI-leverage на учётное время, а не отдачу саморазвития.

Различение: [Учётное ≠ Инвестированное](../../../../../.claude/rules/distinctions.md) (WP-310, 15 мая).

## Фикс (WP-310)

`bh.inv = slot_logged.hours + lesson_completed.duration_minutes / 60`

`coding_time` исключён из расчёта инвестированных часов (FORM.089 §12.2). Из расчёта мультипликатора IWE (PD.FORM.104) — **не** исключён, поскольку там он по назначению.
