---
id: DP.M.018
title: External Data Fallback Hierarchy
type: method
domain: digital-platform
status: active
valid_from: 2026-05-10
schema_version: 1
---

# DP.M.018 — Иерархия fallback для внешних данных

## Назначение

Паттерн для получения данных от внешнего источника, который может быть недоступен.
Трёхуровневая стратегия: прямой CLI → событийная БД → отложенная синхронизация.

## Алгоритм (IPO)

**Вход:** запрос на данные X за дату D от пользователя U
**Процесс:**
1. Попытка через прямой CLI/API внешнего сервиса
2. При недоступности CLI: запрос к событийной БД (`domain_event`), где данные хранятся
   после ночной синхронизации
3. При отсутствии в БД: пометить «pending sync» до следующей синхронизации
**Выход:** данные X или явная пометка о временном отсутствии

## Пример: WakaTime в Day Close

```sql
SELECT payload->>'human_readable', payload->>'total_seconds'
FROM public.domain_event
WHERE event_type = 'coding_time'
  AND account_id = '{DT_USER_ID}'
  AND external_id = 'wakatime:{DT_USER_ID}:{YYYY-MM-DD}'
```

Источник: `learning` БД (Neon). Поле `human_readable` = «9 hrs», `total_seconds` для мультипликатора.

## Применимость

Любой внешний data source с ночной синхронизацией в событийную БД:
WakaTime, Linear, GitHub activity, Calendar.

## Связи

- Pack: `DP.M.001` (knowledge-extraction) — аналогичная idempotent-логика
- DS: `FMT-exocortex-template/.claude/skills/day-close/SKILL.md` — конкретная реализация
