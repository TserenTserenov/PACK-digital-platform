---
id: DP.M.302
name: "Trusted-reference хранилище: immutable контракт + audit-таблица рядом"
type: method
status: draft
created: 2026-06-12
trust:
  F: 3
  G: domain
  R: 0.8
epistemic_stage: established
sources:
  - session-close 2026-06-11, WP-417 Ф3.2 (panel_store.py, wp417-panel-schema.sql, eeaee81d6)
tags: [storage, immutable, audit, trusted-reference, cqrs-adjacent, database]
wp: WP-417
---

# DP.M.302 — Trusted-reference хранилище: immutable контракт + audit-таблица рядом

## 1. Проблема

Когда обещание сервиса — «trusted reference» (одна каноническая запись на сущность, фиксирующая факт), нужно гарантировать: финальная запись не изменяется после финализации, а сырьё расчёта сохраняется для аудита вне контракта.

## 2. Паттерн

Хранилище разбивается на **две таблицы**:

| Таблица | Роль | Контракт |
|---------|------|----------|
| `<entity>_daily` | Контрактная (trusted reference) | `предварительная` → `финальная`; финальная immutable (двойная защита) |
| `<entity>_audit` | Аудитная (вне контракта) | Сырьё расчёта; только для верификации/отладки |

**Доступ потребителей:** только `read_panel(<entity>)` — без side effects.
**Запись:** только воркер через `write_panel(<entity>)`, который отказывает при перезаписи финальной записи.

## 3. Двойная защита финальности

1. **App-level:** `write_panel()` проверяет `status == 'финальная'` → исключение.
2. **DB-trigger:** `BEFORE UPDATE` отклоняет изменение строки с `status = 'финальная'` на уровне БД.

## 4. Различение от CQRS

| CQRS | Этот паттерн |
|------|-------------|
| Read model из event log | Read model = контракт (финальная запись) |
| Event store = воспроизведение | Audit = «follow-the-money», не event sourcing |
| Write ≠ read | Write path — воркер; read path — read_panel |

## 5. Применимость

- Ночные сводки, дайджесты, сертификаты
- Материализованные факты с обязательной историей
- Любой вывод, который «не должен течь» после финализации

## 6. Антипаттерн

Одна mutable таблица → финальный результат перезаписывается рефакторингом → ретроспектива ненадёжна.

## 7. Связи

- DP.M.305 — frozen formula hash — дополнение к этому паттерну
- DP.M.306 — honest tile degradation — применяется при чтении
