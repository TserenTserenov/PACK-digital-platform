---
id: DP.M.196
type: method
title: UPSERT runtime-verify через двойную дельту (timestamp + state)
status: proposed
trust: provisional
epistemic_stage: B
created: 2026-05-27
source: session-transcript 2026-05-27 (WP-330 Ф8.2 runtime smoke 21:08 МСК) + commit 1316ef2 aist_bot_newarchitecture
domains: [runtime-smoke, upsert, verification, testing]
---

# DP.M.196 UPSERT runtime-verify через двойную дельту

## Назначение

Verify-стратегия для runtime-проверки UPSERT-семантики, которая не путает «запись существовала» с «UPSERT реально исполнился».

## Контекст применимости

Любые UPSERT-проверки в боте, ETL, sync-агентах, edit-handlers — везде, где PK совпадает между прогонами и cleanup может быть частичным.

## Failure mode, который метод закрывает

Слабая verify-стратегия `SELECT 1 FROM table WHERE pk=X` подтверждает существование записи, но не подтверждает, что код-путь UPSERT (а не early-return на existing-record) реально исполнился. Запись могла остаться от прошлого прогона при неполной cleanup-логике.

## Метод

Проверять две дельты одновременно:

1. **Timestamp-дельта:** `updated_at` (или другой timestamp) обновился к моменту нажатия/триггера.
2. **State-дельта:** бизнес-поле (state/status/value) приняло ожидаемое новое значение.

### Интерпретация комбинаций

| Timestamp | State | Диагноз |
|-----------|-------|---------|
| Обновился | Обновился | ✅ UPSERT исполнился |
| Обновился | Не изменился | No-op update (тронул строку без изменения сути) |
| Не изменился | Изменился | Клок-проблема или ручная правка |
| Не изменился | Не изменился | UPSERT не выполнился (early-return или другая ветка) |

## Прецедент

WP-330 Ф8.2 (2026-05-27 21:08 МСК): runtime-smoke marathon_state в боте `aist_bot_newarchitecture`. Состояние `chaos (25.05)` → `stuck (21:08)`. Обе дельты подтвердили UPSERT.

## Анти-паттерн

`assert SELECT 1 FROM table WHERE pk=X` — проверяет существование, не свежесть.

## Связи

- **Прецедент:** commit 1316ef2 (`aist_bot_newarchitecture`)
- **Родственный паттерн:** runtime-smoke patterns (general category в 03-methods/)
