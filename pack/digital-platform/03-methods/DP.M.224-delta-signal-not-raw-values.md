---
id: DP.M.224
type: method
title: "Delta-signal vs raw values для periodic review"
status: active
created: 2026-05-30
trust_level: T2
epistemic_stage: validated
domains: [observability, ux, ergonomics]
related:
  - DP.D.114
  - DP.M.223
---

# DP.M.224: Auto-collected delta-signal вместо raw values

## Suspect (когда применять)

UX-паттерн для bi-weekly / monthly review автоматически собираемых метрик (cost, latency, count). Без него: автомат пишет сырые цифры → пилот в день review открывает 3-4 значения предыдущих периодов → вычисляет тренд в голове → принимает решение. ~5 мин per metric × N metrics = серьёзный barrier к регулярному review → review откладывается → метрики бесполезны.

## Алгоритм

1. **Автомат пишет delta-сигнал.** Рядом с raw value добавить колонку Trend: `↑ / ↓ / =`. Пилот смотрит на trend, не считает.
2. **Гранулярность.** 3 состояния (↑ / ↓ / =) — достаточно для bi-weekly решения. 5 состояний (++/+/=/-/--) — избыточны без перехода к графикам.
3. **Без auto-label 🟢/🟡/🔴 для bi-weekly.** Цветовые labels уместны для daily/realtime monitoring; для bi-weekly review = overkill, добавляют шум.
4. **Колонка «Trigger?».** Boolean: достигнут ли threshold? Даёт 5-секундный ответ «нужно ли действовать сейчас?».
5. **Формат строки таблицы.**
   ```
   | Metric | Value | Trend vs 2нед | Trigger? |
   |--------|-------|---------------|----------|
   | Cost/мес | $42 | ↑ | no |
   | Latency p95 | 11s | = | yes (>10s) |
   ```

## Тест применимости

«Требует ли формат >1 мин ручного парсинга от пилота при review?»

- Да → переоформить как delta + trigger boolean.
- Нет → текущий формат ок.

## Антипаттерн

Автомат пишет сырые цифры без trend-колонки → пилот вынужден держать 3-4 значения в голове или открывать предыдущий journal → когнитивная нагрузка делает review tedious → откладывается → метрики становятся write-only.

## Применимость

- WP-244 metrics journal.
- Любой bi-weekly / monthly observability review.
- Cost dashboards.
- KPI tracking без real-time alerting (где визуальные labels избыточны).

## Связи

- **DP.D.114** (continuous trend ≠ point-in-time) — основание: delta-signal работает потому что continuous data уже содержит temporal axis.
- **DP.M.223** (pointer-only fork closure) — соседний governance-метод из той же peer-сессии.

## Источник

Peer-session 2026-05-30-24-fork3-wp-244-observability-unblock (Тема 3): формат WP-244 metrics journal — отвергнут auto-label 🟢/🟡/🔴 в пользу trend + trigger boolean.