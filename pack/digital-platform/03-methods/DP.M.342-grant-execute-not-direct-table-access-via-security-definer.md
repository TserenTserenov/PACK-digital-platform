---
id: DP.M.342
title: "EXECUTE на SECURITY DEFINER функцию вместо прямых прав на таблицу"
type: method
pack: PACK-digital-platform
domain: digital-platform / security-engineering
trust: draft
epistemic_stage: observed
valid_from: 2026-07-05
source: "session-close 2026-07-05 (WP-457 Ф10 peer-сессия, тема 3)"
related:
  see_also: [DP.D.199, DP.M.341, DP.METHOD.115]
tags: [postgresql, security, least-privilege, security-definer, grants, database]
---

# DP.M.342 — EXECUTE на SECURITY DEFINER функцию вместо прямых прав на таблицу

## Описание

Если операция инкапсулирована в функцию с правами определяющего (SECURITY DEFINER), потребителю выдаётся только `GRANT EXECUTE` на функцию — таблица остаётся закрытой, право сужено до конкретной операции.

## IPO

**Input:** воркер/роль, которому нужно выполнить операцию (например, удаление данных по праву на забвение), при наличии существующей SECURITY DEFINER функции для этой операции

**Process:**
1. Проверить: существует ли функция-обёртка для нужной операции? (grep по миграциям / `\df+`)
2. Если да — выдать `GRANT EXECUTE ON FUNCTION func_name(...) TO worker_role`
3. Не выдавать прямые права на таблицу (SELECT/DELETE/UPDATE)

**Output:** минимально необходимый набор прав; таблица закрыта, операция доступна только через функцию

## PostgreSQL-пример

```sql
-- Функция уже существует (SECURITY DEFINER)
-- GRANT EXECUTE, не GRANT SELECT:
GRANT EXECUTE ON FUNCTION delete_user_data_on_erasure_request(uuid)
    TO erasure_worker_role;

-- НЕ делать:
-- GRANT SELECT, DELETE ON domain_event TO erasure_worker_role;
```

## Ограничения (обязательны к учёту)

1. Функция должна сама валидировать параметры — потребитель не видит таблицу напрямую
2. Изменение сигнатуры функции требует обновления GRANT

## Тест применимости

«Есть ли уже функция-обёртка для этой операции?» Да → выдать EXECUTE, не прямые права на таблицу.

## Антипаттерн

Выдать `GRANT SELECT` (или широкий `GRANT ALL`) на таблицу воркеру, которому нужна одна конкретная операция, уже инкапсулированная в функцию.

## Связи

- DP.D.199 (RLS-политика ≠ защита для роли с BYPASSRLS) — смежный принцип правильного применения механизмов защиты
- DP.METHOD.115 (диагностика источника записи через авторизационный слой) — SECURITY DEFINER упомянут в ограничениях метода
