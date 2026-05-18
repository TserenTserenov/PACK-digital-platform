---
id: DP.M.091
name: Scope Guard — enforcement Parliament-модели через enum + schema isolation
kind: Method
status: active
created: 2026-05-18
sources:
  - DP.ROLE.052 §2 (cognitive-proxy-analyst, конкретный instance)
  - captures 2026-05-18 (session-close)
  - HD warm distinction «Парламент ≠ Президент»
related:
  enforces: [DP.D.034]  # Three-Axis Access Control Model
  applies_to: [DP.ROLE.052]
  complements: [DP.D.052]  # Персона / Память / Контекст (boundaries on Память.Derived)
---

# DP.M.091: Scope Guard — enforcement Parliament-модели через enum + schema isolation

## Определение

Метод реализации Parliament-модели доступа (HD warm distinction «Парламент ≠ Президент») для роли, имеющей доступ к данным пилота через memory-провайдер или другой не-детерминированный источник. Границы записи enforced на уровне БД-схемы и Pack-документа роли, **не на уровне промпта LLM-агента**.

## Тест применимости

> «Роль имеет доступ к нескольким доменам данных, должна писать только в часть?»

- **Да** → Scope Guard (этот метод): enum allowed/forbidden + schema isolation
- **Нет** → плоский role-grant без enum

## Отличие от RLS

| Аспект | RLS (Row-Level Security) | Scope Guard |
|--------|---------------------------|--------------|
| Что ограничивает | `WHERE` на чтение строк | `GRANT/REVOKE` на запись + явный enum |
| Где описано | DDL `CREATE POLICY` | DDL + Pack-документ роли (читаемый контракт) |
| Защищает от | Чтения чужих строк | Записи в forbidden поля при jailbreak / prompt injection |

## Шаблон (4 элемента)

1. **Allowed-write enum** — конкретный список полей/таблиц, куда роли разрешено писать (пример из DP.ROLE.052: `cp.wld`, `cp.agt`, `bh.awr`).
2. **Forbidden-write enum** — конкретный список того, куда запрещено, с явным wildcard «все остальные» (пример: `cp.rhy`, `cp.skl`, `cp.iwe`, `cp.int`, `stage`, `certificate`, `cp.*`).
3. **Schema isolation** — отдельная PG-схема (например, `cognitive`) с собственным role-grant. Другие роли парламента (Аттестатор, Points Calculator) не имеют GRANT на эту схему.
4. **Read-only proxy view** — отдельная роль (`cognitive_proxy_reader`) для downstream consumer'ов (Аттестатор, Портной, Диагност). Никто не пишет в чужую схему напрямую.

## Anti-pattern

«Доверять промпту LLM-агента не писать в forbidden поля» — ломается при jailbreak / prompt injection / неудачном semantic roll (роль решает, что forbidden поле тоже нужно «для качества»). Доступ должен отвергаться на уровне платформы (GRANT/REVOKE), а не на уровне инструкции.

## Связь с DP.D.034

Scope Guard — конкретная реализация Three-Axis Access Control Model (DP.D.034): `Scope` = enum полей, `Role` = schema-grant, `Entitlement` = тир доступа к памяти-провайдеру. Без enum-уровня политика остаётся декларативной (промпт-уровень), не enforced.

## Применимость к новым ролям

При добавлении в платформу любой новой роли с доступом к Памяти пилота через нестандартный источник (memory-провайдер Honcho/Mem0, embedding-индекс на разговоры, LLM-extractor из логов):

1. Определить allowed-write enum (что роль ДОЛЖНА уметь записывать).
2. Определить forbidden-write enum (что роль НЕ должна трогать, + wildcard «все остальные»).
3. Создать (или использовать существующую) изолированную PG-схему под роль.
4. Создать read-only proxy view для downstream consumer'ов.
5. Документировать enum в Pack-файле роли (раздел «Scope Guard») как читаемый контракт, не только в DDL.
