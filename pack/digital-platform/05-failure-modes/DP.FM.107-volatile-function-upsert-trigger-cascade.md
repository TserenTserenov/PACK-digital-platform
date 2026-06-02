---
id: DP.FM.107
type: failure-mode
title: VOLATILE-функция в VALUES UPSERT + trigger в ту же таблицу = error 21000
created: 2026-05-30
domains: [postgresql, projection-worker, upsert]
trust: high
epistemic_stage: validated
source: WP-7 rewards-projection (commit 83b54bc multi-domain-projection-worker)
---

# DP.FM.107: VOLATILE-функция в VALUES UPSERT + trigger в ту же таблицу

## Симптом

PostgreSQL error 21000: `cannot affect row a second time`. Возникает при `INSERT INTO X ... VALUES (..., volatile_func(...)) ON CONFLICT DO UPDATE`, когда `volatile_func` через trigger на промежуточной таблице вносит UPDATE в `X` для той же строки.

## Механизм

Cascade-специфика PostgreSQL UPSERT-семантики:

1. Outer command строго single-command — нельзя затронуть одну строку дважды внутри одного `INSERT`/`UPDATE`.
2. VOLATILE-функция в `VALUES` исполняется ДО outer `ON CONFLICT`.
3. Если функция делает `INSERT INTO applied_events`, и на `applied_events` висит `AFTER INSERT` trigger, который `UPDATE target_table` для того же ключа — outer `ON CONFLICT DO UPDATE` приходит в уже-затронутую строку.
4. PostgreSQL отказывает: одна команда не может коснуться одной строки дважды.

## Условия проявления

- ≥1 VOLATILE-функция в `VALUES` команды с `ON CONFLICT DO UPDATE`.
- Функция (прямо или через trigger на промежуточной таблице) пишет в ту же `target_table`, что и outer UPSERT.
- Совпадение ключа конфликта между inner-write и outer-write.

## Тест применимости

«Есть ли VOLATILE-функция в VALUES команды ON CONFLICT DO UPDATE, и пишет ли она (или триггерит запись) в ту же target-table?» Да → ждать 21000 при первой cascade-ситуации.

## Способы устранения

**A. Минимальный fix (split на две команды):**
1. `SELECT volatile_func(...)` в переменную `$N`.
2. `INSERT INTO X ... VALUES (..., $N::numeric) ON CONFLICT DO UPDATE` — outer не вызывает функцию, trigger завершает UPDATE в первой команде, outer INSERT во второй уже не сталкивается.

**B. Архитектурный fix (registration-time validator):**
- В каталоге projection-rules проверять `provolatile = 'v'` для функций в `_set.<col>.src` для UPSERT-paths.
- Запрещать регистрацию правил с VOLATILE-функцией, пишущей в ту же target_table.

## Прецеденты

- WP-7 rewards-projection (2026-05-30, commit 83b54bc multi-domain-projection-worker): 4 bug'а за 2 дня в одной подсистеме (ping crash, non-TimeoutError, sampling oscillation, RPGT cursor stuck) указали на архитектурную хрупкость projection-rules без runtime-проверки VOLATILE в UPSERT.

## Связи

- **Соседний FM:** DP.FM.099 (notify subscription tied to connection) — другая ось multi-writer.
- **Method-сосед:** DP.M.213 (upsert-xmax-insert-detect) — детекция INSERT vs UPDATE, не cascade.