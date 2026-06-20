---
id: DP.ROLE.078
name: Владелец тира (Tier Authority)
type: role-description
status: draft
valid_from: 2026-06-20
summary: "Единственный компонент, который вычисляет и пишет traits.tier. Операционная роль: меняет состояние персоны. Носитель — user-profile-service."
related:
  specializes: [U.RoleAssignment]
  component_of: [DP.ROLE.001]
  realizes: [DP.SC.185]
  uses:
    - DP.ARCH.002   # service tiers semantic (T0-T4)
    - DP.ARCH.005   # persona entity
  supersedes: []
created: 2026-06-20
updated: 2026-06-20
wp: WP-430
decision: ADR-WP430-001
---

# Владелец тира (Tier Authority) — DP.ROLE.078

> # see DP.SC.185
>
> **Kind:** Operational Role — меняет состояние `ory_identity.traits.tier`. Отличие от контрольной: пишет, а не наблюдает.
> **Owner Role:** IWE Platform — носитель: `user-profile-service` (Railway L2-Platform).

---

## 1. Миссия

Быть единственным компонентом, который принимает lifecycle-события (подписка, AI-сигнал, GitHub-сигнал, admin-override) и записывает актуальный тир персоны в `ory_identity.traits.tier`.

Аналогия: нотариус уровня доступа. Все события приходят к нему; он ставит печать один раз. Никто другой не вправе ставить печать напрямую — только читать уже заверенный документ.

---

## 2. Обязанности

| Обязанность | Как выполняется |
|-------------|----------------|
| Принять lifecycle-событие | API-эндпоинт `/tier/signal` (subscription / mcp / github / admin) |
| Вычислить новый тир | По матрице T0-T4: subscription state + pending signals + admin flag |
| Записать в Ory traits.tier | Ory Admin API PATCH identity traits |
| Отдать актуальный тир | `GET /tier` → читатели (шлюз, бот, онбордер) |
| Downgrade по истечению подписки | Subscription expiry webhook → пересчёт → запись |
| Логировать изменения тира | `tier_change_event` в `learning.domain_event` (audit trail) |

**Запрещено:**
- Принимать команды «напрямую установить тир» от бота (только через `/tier/signal` API).
- Читать traits.tier как input для своего вычисления (кольцевая зависимость). Источник — subscription DB + signal store.

---

## 3. Полномочия

- **Единственный** writer в `ory_identity.traits.tier`. Инвариант: grep писателей = 1.
- Право на Ory Admin API PATCH identity.
- Право на запись в `learning.domain_event` (tier_change class).

**Не имеет права:**
- Читать содержимое личных Pack-данных персоны (только tier-релевантные поля).
- Принимать сигналы от компонентов ниже L2 (только platform-level signals).

---

## 4. Входы и выходы

**Входы:**
- `subscription_event`: {user_id, type: activated|expired, tier: T2}
- `mcp_signal`: {user_id, type: connected|disconnected}
- `github_signal`: {user_id, type: connected|disconnected}
- `admin_override`: {user_id, tier: T0-T5, reason}

Pending-сигналы хранятся в `user_tier_signals`: (user_id, signal_type, received_at, status: pending|applied).
Применяются при следующем lifecycle-событии (например, активация подписки).

**Выходы:**
- `traits.tier` в Ory: T0 | T1 | T2 | T3 | T4 | T5
- `GET /tier` response: {user_id, tier, updated_at}
- `tier_change_event` в learning.domain_event: {user_id, from_tier, to_tier, trigger, timestamp}

---

## 5. Связи с другими ролями

| Роль | Отношение |
|------|-----------|
| Шлюз (Gateway) | Читает `GET /tier`, передаёт mcp-signal и github-signal через API |
| Бот | Читает `GET /tier`, НЕ пишет traits |
| projection-worker (WP-270) | Читает `GET /tier` для own projections, НЕ пишет traits |
| Onboarder (DP.ROLE.067) | Читает `GET /tier` для определения T1→T2 transition |
| Billing | Отправляет subscription_event при активации/истечении подписки |

---

## 6. Бывшие носители (устаревшие писатели)

После реализации Ф3 WP-430 следующие компоненты теряют право записи:

| Компонент | Что делал | После |
|-----------|-----------|-------|
| projection-worker | Писал traits.tier по tier_changed event | Читает GET /tier |
| aist-bot `_persist_tier` | Вычислял тир, писал напрямую | Удалён |
| aist-bot `update_user_tier` | Дублёр, под DISABLE_BOT_TIER_SYNC | Удалён |
| gateway user-path.ts | Писал traits при mcp-signal/github-signal | Делегирует API |
| TEMP-костыль WP-406 Ф14 | `is_dt = telegram OR traits-signal` | Удалён |
