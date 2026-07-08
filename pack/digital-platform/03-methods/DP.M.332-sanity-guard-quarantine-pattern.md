---
id: DP.M.332
name: "Sanity-guard quarantine pattern"
name_ru: "Паттерн карантина sanity-guard: аномалия → статус карантина + инцидент-запись"
name_en: "Sanity-guard quarantine: anomaly gets quarantine status and durable incident record"
summary: "Guard срабатывает двухслойно: (1) аномальная запись получает статус карантина, не финальный статус; (2) каждое срабатывание оставляет durable-след в инцидент-таблице. Уведомление эфемерно, инцидент-запись — нет. Решение о финализации отделено от детекции."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: data-quality
valid_from: 2026-07-04
related:
  see_also: [DP.FM.186, DP.M.333]
tags: [sanity-guard, quarantine, data-quality, incident-table, append-only, durable-trail]
source: "WP-454 Ф5 peer-сессия, session-close 2026-07-03"
schema_version: 1
---

# DP.M.332 — Паттерн карантина sanity-guard

## Описание

Sanity-guard для append-only хранилища проектируется двухслойно:
1. **Карантин, не финальный статус** — аномальная запись помечается «требует проверки»; решение о финализации отделено от детекции.
2. **Durable инцидент-запись** — каждое срабатывание guard'а создаёт запись в отдельной инцидент-таблице (не только уведомление).

Это уточняет более раннее DP.FM.186: сам guard тоже требует проектного решения.

## Algorithm

### Step 1: Детектировать аномалию

Порог guard'а срабатывает на входящую запись (отклонение от медианы, нулевое значение в не-нулевом домене, значение вне допустимого диапазона).

### Step 2: Карантин — не отказ и не пропуск

```sql
-- Записать в хранилище со статусом карантина, не отбрасывать и не принимать финально
INSERT INTO metrics (..., status = 'quarantined', review_required = true)
```

### Step 3: Создать durable инцидент-запись

```sql
INSERT INTO metric_incidents (ts, source, anomalous_value, reason, status)
VALUES (now(), 'panel_worker', :value, 'sigma_threshold_exceeded', 'open')
```

Уведомление (Telegram-алерт) — эфемерно, пропадёт. Инцидент-запись — durable.

### Step 4: Финализировать отдельно

Ревью инцидента → принять значение / скорректировать / отклонить. Финализация не автоматическая.

## When to Use

- Append-only хранилища с финальными значениями (DP.FM.186)
- Метрики, где аномалия может быть валидной (системный сбой → законный ноль)
- Любой расчётчик, где порог срабатывания не различает «данные некорректны» и «данные нестандартны»

## Тест применимости

«Guard может ошибочно отклонить реальное значение?» Да → карантин обязателен (аномалия ≠ ошибка). «Уведомление — единственный след срабатывания?» Да → нужна инцидент-таблица.

## Связи

- DP.FM.186 (append-only без sanity-guard) — complementary: DP.FM.186 описывает failure mode при отсутствии guard'а; этот метод описывает как guard должен быть спроектирован
- DP.M.333 (матрица режимов отказа по типам событий) — ортогонально: там fault-handling strategy, здесь detection layer
