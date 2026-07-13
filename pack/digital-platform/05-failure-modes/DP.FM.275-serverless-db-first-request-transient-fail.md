---
id: DP.FM.275
name: "Serverless DB cold-start: первый запрос после паузы транзиентно падает"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-13
source: "session-close 2026-07-09; payment-registry INCIDENT-2026-07-08 (commit dad0baa)"
related:
  see_also: ["DP.FM.039: zero-data cold-start (другой аспект: данные vs доступность)"]
tags: [neon, serverless, cold-start, transient-error, retry, postgresql]
---

# DP.FM.275 — Serverless DB: первый запрос после пробуждения транзиентно падает

## Паттерн

Serverless PostgreSQL (Neon, PlanetScale, Supabase pause) приостанавливает compute при простое >5 мин.
Первый запрос после пробуждения транзиентно падает: «relation does not exist», connection refused,
или timeout — схема и соединение ещё не готовы.

Симптом неочевиден: ошибка выглядит как **логическая** («нет таблицы»), а не **инфраструктурная** (cold-start).

## Диагностика

**Тест Neon cold-start vs реальная ошибка:**
«Ручной прогон той же команды через 1-2 мин — успешен?» → Да = cold-start, не логическая ошибка.

**Сигнал:** Один и тот же health-check периодически падает, затем проходит без изменений в коде.

## Fix

Retry 3x с паузой 30s вокруг psql-запроса:

```bash
for attempt in 1 2 3; do
  if psql -v ON_ERROR_STOP=1 "$DATABASE_URL" <<PSQL
SELECT 1 FROM subscription.contract LIMIT 1;
PSQL
  then
    break
  fi
  echo "Attempt $attempt failed, retrying in 30s..."
  sleep 30
done
```

## Инцидент

INCIDENT-2026-07-08 (payment-registry, commit dad0baa):
health-check падал 3 раза за 12ч с «relation does not exist»; ручной retry через минуту — успешен.

## Применимость

Любой serverless / auto-pause PostgreSQL: Neon, PlanetScale, Supabase (pause mode), AWS Aurora Serverless v1.
