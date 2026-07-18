---
id: DP.FM.338
name: Month-Range Set Filter Drops Middle Months
category: date-range
severity: major
status: draft
summary: "set{from.month, to.month} фильтрация пропускает средние месяцы при 30-дневном окне, охватывающем 3+ календарных месяца. Исправление: walk с инкрементом месяца и year rollover."
created: 2026-07-18
valid_from: 2026-06-27
related:
  see_also: []
tags: [date-range, calendar, month-filter, silent-data-loss]
source: "session-transcript 2026-06-26 WP-447 §6 (Kimi correction turn 03-peer) + commit 6e3bbb950; extraction-report 2026-06-27-inbox-check-5 #1"
schema_version: 1
---

# [DP.FM.338] Month-Range Set Filter Drops Middle Months

## Суть паттерна

Коарсная фильтрация `set{date_from.month, target_date.month}` теряет средние месяцы при 30-дневном окне, охватывающем 3+ календарных месяца. Например, диапазон 2026-01-31 → 2026-03-01 формирует множество `{1, 3}`, оставляя февраль (месяц 2) вне фильтра — все объекты февраля тихо отбрасываются.

## Механизм

1. Строится множество `{date_from.month, target_date.month}`.
2. Для диапазона, охватывающего N > 2 месяцев, промежуточные месяцы в множестве отсутствуют.
3. Объекты с датами в промежуточных месяцах тихо пропускаются без exception или предупреждения.

## Диагностика

**Тест:** «Может ли диапазон пересекать 3 и более месяца?» → Да → set-фильтрация ненадёжна, нужен walk.

## Исправление

Заменить set-фильтрацию на явный walk от `from.month` до `to.month` с инкрементом и year rollover (`12 → 1`).

```python
# Broken
months = {date_from.month, target_date.month}

# Fixed
months = set()
y, m = date_from.year, date_from.month
while (y, m) <= (target_date.year, target_date.month):
    months.add(m)
    m += 1
    if m > 12:
        m, y = 1, y + 1
```

## Применимость

- Обход директорий, именованных по `YYYY-MM/`
- Любой код, фильтрующий объекты по полю `date.month` в диапазоне
- Batch processing с временными окнами > 28 дней, пересекающими границу месяца
