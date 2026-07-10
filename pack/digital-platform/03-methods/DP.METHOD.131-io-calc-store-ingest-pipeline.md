---
id: DP.METHOD.131
name: IO/calc/store ingest pipeline pattern
name_ru: Трёхслойный IO/calc/store для конвейера телеметрии из внешнего источника
type: method
status: established
summary: "Трёхслойный паттерн для идемпотентного data-ingest из внешних источников: IO-слой (чтение сырых данных), calc-слой (нормализация/агрегация без хранения), store-слой (запись только при не-None; transient-контракт: None = 'нет данных за период' → пропустить, не писать нули). Дополнительно: chunking батчей против таймаутов внешнего API."
created: 2026-07-09
trust:
  F: 2
  G: domain
  R: 0.85
epistemic_stage: established
related:
  realized_by: [panel_wakatime.py, panel_calc.py, panel_store.py]
  complements: []
tags: [ingest, telemetry, data-pipeline, idempotency, io-calc-store, chunking]
wp: WP-469
sources:
  - session-transcript 2026-07-06
  - git diff DS-my-strategy commit 5b8da8c52
---

# DP.METHOD.131 — Трёхслойный IO/calc/store для конвейера телеметрии

## Контекст

При сборе данных из внешнего источника (API) нужна устойчивость к временным сбоям и идемпотентность: повторный запуск не должен записывать дубли или нули.

## Три слоя

1. **IO-слой** — забирает сырые данные из внешнего источника. Возвращает None если данных нет за период.
2. **calc-слой** — нормализует и агрегирует без хранения. Принимает результат IO, возвращает готовое для записи или None.
3. **store-слой** — пишет только при не-None. **Transient-контракт:** None = «нет данных за период» → пропустить запись, не писать нули.

**Дополнительный паттерн:** chunking — разбить большой период на батчи для обхода таймаутов внешнего API.

## Инварианты

- store не вызывается при None (защита от нулевых записей)
- calc не обращается к хранилищу
- IO не агрегирует и не решает что писать

## Эталонная реализация

WakaTime-конвейер WP-417: `panel_wakatime.py` (IO) / `panel_calc.py` (calc) / `panel_store.py` (store).

## Применимость

Любой data-pipeline из внешнего источника (Health API, Calendar, Wakatime, sleep tracker) где нужна идемпотентность и защита от нулевых записей при временном отсутствии данных.
