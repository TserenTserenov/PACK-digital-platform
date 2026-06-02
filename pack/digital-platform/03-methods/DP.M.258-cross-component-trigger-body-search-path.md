---
id: DP.M.258
type: method
title: Cross-component trigger reasoning через полное body + search_path resolution
created: 2026-05-30
domains: [postgresql, debugging, verification]
trust: high
epistemic_stage: validated
source: WP-7 rewards-projection peer-session 2026-05-30-16
---

# DP.M.219: Cross-component trigger reasoning через полное body + search_path resolution

## Назначение

Метод диагностики cascade-багов, где взаимодействуют функция + trigger + промежуточная таблица в разных (или казалось бы разных) схемах. Снижает частоту ложно-отрицательного диагноза «cascade невозможен — schemas разные».

## Шаги

1. **Не доверять сигнатуре функции** для вывода «куда она пишет». Сигнатура показывает аргументы и return type, но не side effects.

2. **Получить полное body компонента:**
   - PostgreSQL: `\df+ <function>` в `psql` или `SELECT pg_get_functiondef(oid)` через `pg_proc`.
   - Trigger: `pg_get_triggerdef(oid)` через `pg_trigger`.
   - Не одну строку CREATE TRIGGER — полный текст.

3. **Выписать каждый side-effect statement** (`INSERT`, `UPDATE`, `DELETE`, `NOTIFY`, `CALL`) из body. Включая те, что внутри `IF`/`CASE`/`LOOP`.

4. **Для каждого unqualified table reference** (имя без `schema.`) определить эффективный `search_path` в контексте вызова:
   - Из application: какой `search_path` в роли приложения? (`SHOW search_path`)
   - Из миграции: явный `SET search_path` в скрипте?
   - Из trigger в schema X: `search_path = schema_of_trigger, public`?

5. **Только после (1)-(4)** утверждать «функция пишет в X, trigger на Y, schemas разные → cascade невозможен».

## Антипаттерн

Argue cascade-невозможность на основе сигнатур + одной строки CREATE TRIGGER. Даёт ложно-отрицательный диагноз, пропускает реальный root cause. Особенно опасно когда unqualified references маскируют реальное место записи.

## Тест применимости

«Есть в системе trigger + функция + промежуточная таблица?» Да → перед утверждением «cascade невозможен» применить метод. Применимо к: PostgreSQL trigger debugging, cross-schema FDW, любая система с namespace-resolution (Python imports relative vs absolute, Kubernetes namespaces, Terraform modules).

## Прецедент

WP-7 rewards-projection (2026-05-30, peer-session 2026-05-30-16, Тема 4): писатель утверждал «schemas разные → cascade невозможен» по сигнатурам. Кими в роли peer-критика поймал неверификацию: `rewards.applied_events` (упомянуто) ≠ `public.applied_events` (фактическое trigger-target) — но references в body функции unqualified, search_path = public → совпадение. Метод подтвердил cascade.

## Связи

- **Failure mode:** DP.FM.107 (VOLATILE-функция в VALUES UPSERT + trigger) — корректная диагностика через этот метод.
- **Соседний method:** DP.M.213 (upsert-xmax-insert-detect) — детекция INSERT vs UPDATE, не cascade.