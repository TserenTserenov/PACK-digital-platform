---
id: DP.FM.168
name: "Метрика=0 для активного пользователя: code-review фильтра до проверки raw-данных"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-19
source: "session 2026-06-18, WP-149 E2E, bug 7304f2c16"
related:
  references: [DP.FM.084]
tags: [diagnostics, metric, identity, emit, account-id, filter, data-integrity]
---

# DP.FM.168 — Метрика=0 для активного пользователя: code-review фильтра до проверки raw-данных

## Паттерн

Метрика показывает 0 у пользователя, который явно выполнял нужные действия. Разработчик начинает с code review фильтра (`event_type`, SQL WHERE) — теряет время, фикс не помогает, потому что реальная причина — отсутствие данных в raw-таблице (identity mismatch или missing emit).

## Механизм ошибки

Два возможных первоисточника дают одинаковый симптом «метрика=0»:
1. **Identity mismatch** — `account_id` в метрике ≠ `account_id` в событиях (разные идентификаторы: Ory UUID vs aisystant_id vs int)
2. **Emit gap** — событие генерируется, но не долетает до таблицы (нет вызова emit, неверный endpoint, silent HTTP error)

Оба выглядят как «filter не работает», хотя filter правильный.

## Правильный порядок диагностики

1. **Raw-check первым:**
   ```sql
   SELECT count(*) FROM learning.domain_event
   WHERE account_id = '<X>'
     AND event_type = 'day_plan_closed';
   ```
   - `count = 0` → identity / emit issue, **не filter**
   - `count > 0` → переходить к проверке filter

2. **Identity-path (при count=0):**
   - Проверить, какой идентификатор используется в emit (`account_id`) vs в метрике
   - Сверить `public.users` (ory_id ↔ aisystant_id ↔ legacy_id)

3. **Filter (только при count>0):**
   - Проверить `event_type` в запросе vs `event_type` в данных
   - Проверить диапазоны дат, статусы

## Тест обнаружения

«Есть ли хотя бы одно событие нужного типа в raw-таблице за период?»
- Нет → identity/emit issue (code review фильтра не поможет)
- Да → filter issue

## Связи

- **DP.FM.084** (event_type mismatch → reward_rules catalog drift) — там catalog не синхронизирован, здесь emit gap или identity mismatch; оба класса приводят к «событие принято, эффект не считается»

## Контекст

WP-149 E2E: руководство отображало «0/30 дней с закрытым Day Plan» для пилота с доказанной активностью. Диагностика: all_time=0 для ВСЕХ account_id → подтверждение identity / emit issue, не filter bug. Источник: bug-2026-06-18-day-plan-metric.md, commit 7304f2c16.
