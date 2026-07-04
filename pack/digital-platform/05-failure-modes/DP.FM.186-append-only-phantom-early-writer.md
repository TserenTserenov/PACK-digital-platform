---
id: DP.FM.186
title: "Append-only хранилище без sanity-guard: phantom early-writer фиксирует аномальную запись навсегда"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / data-platform
epistemic_stage: confirmed
valid_from: 2026-07-04
source: "session-close 2026-07-03 (WP-454 Ф5, bug-2026-07-03-panel-worker-nightly-race-undercounts.md)"
related:
  see_also: [DP.ARCH.006, DP.D.183]
---

# DP.FM.186 — Append-only хранилище без sanity-guard: phantom early-writer фиксирует аномальную запись навсегда

## Описание

Хранилище с семантикой `final=true` / immutable принимает запись от произвольного процесса раньше штатного расчётчика. Штатный расчётчик находит запись уже существующей → выполняет idempotent no-op. Аномальное значение остаётся навсегда.

## Пример

WP-454: панель параллелизма хранится в Neon (append-only, `final=true` флаг). Неизвестный процесс (~01:00 UTC) записал «финальный» результат с `human_active_h=0.0` — заниженным на два порядка. Штатный расчётчик (04:15 UTC) нашёл запись → idempotent no-op → ноль навсегда.

## Тест обнаружения

«При записи "final" — проверяется ли отклонение значения от исторической медианы за последние N дней?» Нет → уязвимость к phantom early-writer присутствует.

## Инвариант

Append-only / final semantics не защищают от phantom early-writer. Sanity-guard на входе (отклонение > σ от медианы) — необходимое условие, не опциональная защита.

## Митигация

1. Перед записью «final»: guard-check — отклонение от медианы последних N дней
2. При отклонении > порога: не писать → создать инцидент-запись → эскалация (не тихий skip)
3. Мониторинг: alert при `writer != штатный расчётчик` для записей с `final=true`

## Связи

- DP.ARCH.006 (Память: Observed события + Derived агрегаты) — архитектурный контекст хранилища
- DP.D.183 (Машинный ноль ≠ результат измерения «ноль») — смежный паттерн диагностики этого FM
