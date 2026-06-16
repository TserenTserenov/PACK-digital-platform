---
id: DP.D.133
name: Три уровня изоляции данных в IWE
type: domain-entity
subtype: distinction
status: active
summary: "Данные в IWE изолируются на трёх независимых уровнях: БД-уровень (vault-паттерн), schema-уровень (aisystant schema), table/column-уровень (RLS + column grants). Каждый уровень защищает от разного класса нарушений. Уровни не заменяют друг друга — нарушение одного не компенсируется другим."
created: 2026-06-16
valid_from: 2026-06-16
trust:
  F: 3
  G: domain
  R: 0.85
epistemic_stage: established
related:
  requires: [DP.KR.030]
  uses:
    - DP.ARCH.004    # vault-паттерн §10.12
    - DP.D.034       # access control model
    - DP.D.059       # три класса хранения credentials
tags: [data-isolation, rls, schema, vault, security, pii]
---

# Три уровня изоляции данных (DP.D.133)

## 1. Проблема

Без явной изоляции данные разных пользователей или контуров могут «просочиться» через любой из трёх каналов:
- По **доступности инстанса** (кто может подключиться к БД вообще)
- По **видимости схемы** (кто видит таблицы внутри одного инстанса)
- По **строкам и столбцам** (кто видит конкретные записи внутри одной таблицы)

Ошибка: считать, что один уровень «покрывает» другой. Сильный RLS не помогает, если shared-БД утекает через плохо настроенный connection pooler.

## 2. Три уровня изоляции

| Уровень | Метод | Кто защищает | Артефакт IWE | Тест верификации |
|---------|-------|-------------|--------------|-----------------|
| **L1: БД-vault** | Отдельный инстанс/БД для compliance-критичных данных | Neon project isolation | DP.ARCH.004 §10.12: `payment_registry` (PCI), `secrets` (OAuth) — отдельные БД | `neon projects list` → `payment_registry` и `secrets` в отдельных project_id |
| **L2: Schema** | REVOKE/GRANT на schema; данные пользователей в schema `aisystant`, не в `public` | Neon role policies | B7.3.1 (классификация схем) + B7.3.3 (schema grants) | `SELECT nspname, nspacl FROM pg_namespace WHERE nspname='aisystant'` — проверить отсутствие `=UC` у анонимных ролей |
| **L3: Row/Column** | RLS-политики + column-level grants | PostgreSQL / Neon Authorize | B7.3.3 column grants + B7.3.5 Fernet-шифрование сенситивных полей; RLS-матрица в WP-245 Ф26 | `SELECT * FROM pg_policies WHERE tablename='learning_events'` + `SELECT * FROM information_schema.column_privileges WHERE grantee='web_anon'` |

## 3. Маппинг на существующие артефакты

```
L1 vault-паттерн  →  DP.ARCH.004 §10.12
                     Rationale: платёжные данные (PCI) и секреты (OAuth) не должны
                     жить в той же БД, что и пользовательские события

L2 schema-level   →  B7.3.1 «классификация данных по чувствительности»
                     B7.3.3 «REVOKE/GRANT на схемы»
                     Тест: `search_path` сервисных ролей содержит только aisystant,
                     не public; cross-schema read требует explicit GRANT

L3 row/column     →  B7.3.3 column grants для PII-полей (email, telegram_id)
                     B7.3.5 Fernet-шифрование для высокочувствительных полей
                     Neon Authorize: JWT → Neon RLS автоматически применяет
                     пользовательский контекст к запросам
```

## 4. Различение

**Изоляция данных ≠ контроль доступа (DP.D.034):** DP.D.034 описывает «кто и как запрашивает доступ» (AuthZ логика, scopes, consent). DP.D.133 описывает «где данные физически или логически разделены». Можно иметь сильный AuthZ и слабую физическую изоляцию (все данные в одной БД без RLS).

**Изоляция данных ≠ хранение credentials (DP.D.059):** DP.D.059 описывает классы хранения учётных данных (env/config/PG USER MAPPING). DP.D.133 описывает разделение пользовательских данных по уровням БД.

**Тест «уровни не заменяют»:** если L3 (RLS) настроен идеально, но L1 (vault) нарушен — DBA-роль читает все таблицы напрямую, минуя RLS (via `SET ROLE bypass`). Верен и обратный случай: L1 корректен, L3 отсутствует — каждый authenticated пользователь видит все строки.

## 5. Инварианты

1. **Compliance-критичные данные → L1.** PCI (платежи), OAuth-секреты — в отдельных Neon project_id, не в `learning` БД.
2. **Сервисные роли → L2.** Сервисные роли (service_role, web_anon) имеют явный REVOKE на схему `public`, GRANT только на `aisystant`.
3. **Пользовательские данные → L3.** Таблицы с PII (`email`, `telegram_id`, `user_events`) защищены RLS-политиками; чтение возможно только по `auth.uid()` или явному superuser.
4. **RLS bypass ≠ норма.** Роли с `BYPASSRLS` — только специализированные backend-роли, перечисленные в B7.3.3 §4. Audit-ролям (R23-R26) BYPASSRLS не выдаётся.

## 6. Связи

- `requires`: [DP.KR.030] — физическая изоляция реализует инвариант разделения учёт/доступ/аудит
- `uses`: [DP.ARCH.004 §10.12, DP.D.034, DP.D.059, B7.3.1, B7.3.3, B7.3.5]
- `refined_by`: KR.032 (Принцип управления доступом) — при создании отдельного РП
