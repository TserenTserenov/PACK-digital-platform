---
id: DP.SC.128
name: Ingest активности клуба (Discourse)
type: sc
status: draft
layer: L2
summary: "Платформа получает события активности участников клуба systemsworld.club для расчёта баллов и аналитики"
consumer: R-Стратег, R-Аналитик когорт, rewards-projection-worker, бот Aist
created: 2026-05-07
updated: 2026-05-07
related:
  realizes: [DP.ROLE.036]
  uses: [DP.SC.001-event-gateway, DP.ARCH.004]
  extends: []
wp: WP-296
---

# [DP.SC.128] Ingest активности клуба (Discourse)

## Правило (инвариант)

- События клуба пишутся в `domain_event` с `source='club_discourse'` и `external_id=<discourse_event_id>`.
- Идемпотентность: `ON CONFLICT (source, external_id) DO NOTHING` — повторный приём не дублирует.
- Резолв `discourse_user_id → ory_identity_id` — **lazy** (через bridge-таблицу `club.members`); пока не резолвлено, `account_id IS NULL`. События не теряются.
- Коннектор НЕ модифицирует Discourse (read-only). Не пишет посты, не лайкает, не удаляет.
- PII: email участников клуба не сохраняется в Aisystant (B7.3 / B7.3.1). Bridge — только `discourse_user_id ↔ ory_identity_id` через ORY API после включения ORY-SSO в клубе.

## Обещание

**Кому:** Платформа Aisystant (rewards-projection-worker, бот, Метабаза).

**Зачем:** Активность участников клуба невидима для платформы → невозможно: (а) начислять баллы за вклад в клуб; (б) видеть когортную вовлечённость; (в) отправлять нудж-сообщения неактивным.

**Что получит:**

| Блок | Содержимое |
|------|------------|
| **События клуба в `domain_event`** | `source='club_discourse'`, `external_id`, `event_type` (post_created, like, topic_created, …), `payload` (category_id, topic_id, post_id, discourse_user_id), `occurred_at` |
| **Bridge-таблица** | `club.members(discourse_user_id PK, ory_identity_id, linked_at)` — заполняется после ORY-SSO в клубе |
| **Health-сигнал** | `health.connector_status` для коннектора: lag, last_event_id, error_count |

## Триггеры

| Событие | Источник |
|---|---|
| Discourse webhook (post/topic/like/notification) | Push: Discourse → CF Worker endpoint |
| Polling backfill (история, восстановление после downtime) | Cron: каждые 5 мин чек cursor `last_event_id` |
| ORY-SSO включён в клубе | Manual trigger (one-time): backfill bridge `club.members` |

## Входы

- Discourse REST API (`https://systemsworld.club/`, токен `~/.secrets/club_api_token`, scope read-only)
- Discourse webhook payload (HMAC-SHA256 signature через `WEBHOOK_SECRET`)
- ORY API (после включения ORY-SSO в клубе) — для backfill bridge

## Выходы

- `domain_event` (БД #3 activity-hub) — все события клуба
- `club.members` (БД? решение ArchGate Ф2) — bridge `discourse_user_id ↔ ory_identity_id`
- `health.connector_status` (БД #8 health) — лаг, ошибки коннектора

## Время отклика

- Webhook → `domain_event`: ≤ 2 сек p99
- Polling lag: ≤ 5 мин (cron interval)
- Backfill 63K постов: одноразовая операция ≤ 60 мин (rate limit Discourse 60 req/min)

## Режим отказа

| Сбой | Поведение |
|---|---|
| Webhook endpoint недоступен | Discourse retry 3 раза → polling backfill подбирает за следующий cron tick |
| Discourse API недоступен (5xx, timeout) | Polling skip tick, alert через `health.connector_status` после 3 подряд неудач |
| HMAC mismatch на webhook | 401 + log + alert (потенциальная атака или ротация секрета) |
| Cursor advance без INSERT (silent fail, см. feedback_silent_projection_fail) | Cross-DB diff alerter (#8 правило 4) |
| ORY-SSO в клубе откладывается | События копятся с `account_id IS NULL`, баллы не начисляются — состояние «ждём ORY», не сбой |

## Метрики

- `club_events_ingested_total{event_type}` — counter
- `club_events_resolved_total` — те, у кого `account_id IS NOT NULL`
- `club_ingest_lag_seconds` — `now() - max(occurred_at)`
- `club_bridge_coverage_ratio` — `count(resolved) / count(total members)`

## Не входит

- Запись в Discourse (read-only коннектор)
- Email участников клуба (PII tax = 0)
- Интерпретация событий (правила баллов — отдельная роль rewards-projection-worker)
- Real-time сторонние интеграции (только write в Neon)

## Сценарии использования

### Сценарий 1: Расчёт баллов за вклад в клуб
**Потребитель:** rewards-projection-worker (DP.ROLE.??).
**Триггер:** Новое событие `post_created` в `domain_event`.
**Поток:** Webhook → коннектор пишет в `domain_event` → projection-worker читает по cursor → применяет правила (по category_id) → пишет в `rewards.points_ledger` с `account_id` (если резолвлен).
**Результат:** Пользователь видит +N баллов в `/me` бота за активность в клубе.

### Сценарий 2: Когортная аналитика вовлечённости
**Потребитель:** R-Аналитик / Метабаза.
**Триггер:** Запрос «активность когорты "Майские волонтёры" за последние 7 дней».
**Поток:** SQL JOIN `domain_event` × `club.members` × `aist_user` → агрегаты (постов/лайков/notifications per user в когорте).
**Результат:** Дашборд: D7-retention в клубе, топ-вовлечённые, hold-out молчуны.

### Сценарий 3: Нудж неактивных участников клуба
**Потребитель:** WP-117 nudge-система.
**Триггер:** Пользователь не зашёл в клуб ≥14 дней (нет событий с его `discourse_user_id`).
**Поток:** Cron-проверка `domain_event` → если empty → бот шлёт TG-сообщение «давно не было видно в клубе».
**Результат:** Возврат пользователя в активность; снижение churn.

## Promotion plan

L2 → реализация на Ф4. Промоция в Pack staging → L1 не требуется (это L2 с самого начала).
