---
id: DP.FM.290
name: "Apple Health resting_heart_rate: несколько строк на дату — выбирать по received_at DESC"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-14
source: "session-close 2026-07-09; peer-session 2026-07-09-08-day-open-sleep-correction; commit c254cec (extensions/day-open.summary-extra.sh)"
related:
  see_also:
    - "DP.FM.291: промпт не мигрирован при переезде протокола (смежный pattern тихой ошибки данных)"
tags: [apple-health, health-db, sqlite, deduplication, multiple-rows, received_at, resting-heart-rate]
---

# DP.FM.290 — Apple Health: несколько строк resting_heart_rate на дату, выбор по received_at

## Паттерн

Apple Watch пересчитывает дневной агрегат `resting_heart_rate` и отправляет уточнённое значение позже. В базе `health.db` для одной даты может быть **несколько строк** с разными значениями. Наивный запрос (`MIN(qty)`, `ORDER BY date DESC LIMIT 1`) может вернуть устаревшую ревизию.

## Диагностика

**Тест:** `SELECT date, COUNT(*) FROM health WHERE metric='resting_heart_rate' GROUP BY date HAVING COUNT(*) > 1` — вернёт строки = нормально, а не ошибка.

Симптом обнаружения баги: показание за вчера несколько дней подряд одинаковое, хотя Apple Health на телефоне показывает другое.

## Корень

Apple Health использует `received_at` как вектор времени уточнения агрегата, а не строит один неизменный агрегат на дату. При каждом уточнении — новая строка, не UPDATE существующей.

## Fix

```sql
SELECT qty
FROM health
WHERE metric = 'resting_heart_rate'
  AND date >= date('now', '-1 day')
ORDER BY received_at DESC
LIMIT 1
```

**Правило:** `ORDER BY received_at DESC LIMIT 1` — берём самую свежую ревизию.

## Применимость

Аналогичный паттерн применим к другим агрегируемым метрикам Apple Health, которые могут пересчитываться: HRV, step count, sleep stages.

## Отличия от смежных FM

| Ситуация | FM |
|----------|----|
| Apple Health: несколько строк на дату (этот) | DP.FM.290 |
| SQLite date() не парсит нестандартный ISO-8601 offset | соседний FM в пачке (pending) |
