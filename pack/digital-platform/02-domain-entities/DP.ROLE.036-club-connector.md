---
id: DP.ROLE.036
name: Коннектор клуба
type: role-description
status: draft
valid_from: 2026-05-07
summary: "Носитель потока данных systemsworld.club (Discourse) → Neon. Read-only ingest активности участников через webhook + polling backfill, с lazy-резолвом discourse_user_id ↔ ory_identity_id после ORY-SSO."
related:
  specializes: [U.RoleAssignment]
  realizes: [DP.SC.128]
  uses:
    - DP.SC.020   # event-gateway как target writer
    - DP.ARCH.004 # карта Neon
external_dependency: "Discourse REST API + webhooks (https://systemsworld.club/). ORY API (после включения ORY-SSO в клубе)."
created: 2026-05-07
updated: 2026-05-07
wp: WP-296
---

# Коннектор клуба (DP.ROLE.036)

> **Kind:** Infrastructure Role (инфраструктурная роль, носитель потока внешних событий).
> **Owner Role:** Платформенная команда (Архитектор + R5 как стейкхолдер аналитики).

## 1. Миссия

Быть **единственным каналом** проникновения событий клуба `systemsworld.club` в платформу. Гарантировать: каждое событие клуба (post, like, topic, notification) попадает в `domain_event` хотя бы один раз, без дубликатов, с минимальным lag'ом, без потерь при downtime.

**Граница:** read-only ingest. НЕ начисляет баллы (это rewards-projection-worker), НЕ интерпретирует события (правила баллов — отдельный слой), НЕ пишет в Discourse.

## 2. Обязанности

| Обязанность | Метод | Триггер |
|-------------|-------|---------|
| Приём webhook от Discourse | POST endpoint на CF Worker, HMAC-SHA256 verify | Real-time (push) |
| Запись события в `domain_event` | INSERT с `source='club_discourse'`, `external_id`, idempotent ON CONFLICT | После HMAC verify |
| Polling backfill истории | GET `/posts.json?before=<cursor>` paginated | Cron 5 мин (для catch-up при downtime) + одноразовый при первом подключении |
| Курсор `last_processed_event_id` | UPDATE single-row state в `club.cursor` | После каждого batch'а |
| Резолв `discourse_user_id → ory_identity_id` | SELECT `club.members` lookup при INSERT в `domain_event` | На каждое событие |
| Backfill bridge `club.members` после ORY-SSO | One-time call ORY API → INSERT/UPDATE club.members | Manual trigger (когда ORY включится) |
| Health-сигнал в `health.connector_status` | UPDATE lag, last_event_id, error_count | Каждый tick |
| Ретрай при transient fail (Discourse 5xx, timeout) | Exponential backoff 1s/2s/4s/8s, max 3 | На fail |
| Алерт при 3+ подряд fail | TG через AIST Bot | На детекции |

## 3. Полномочия

- **Пишет** в `domain_event` (#3 activity-hub) с `source='club_discourse'` (единственный writer для этого source).
- **Пишет** в `club.members` и `club.cursor` (единственный writer этой схемы).
- **Пишет** в `health.connector_status` (свою строку).
- **Читает** `persona.ory_identity` для резолва email→ory_id (одноразово при backfill bridge после ORY-SSO).
- **Читает** Discourse REST API (read-only scope: `topics:read`, `posts:read`, `users:read`, `categories:read`, `notifications:read`).
- **Не модифицирует** Discourse (read-only).
- **Не интерпретирует** содержимое (правила баллов — rewards-projection-worker).

## 4. Границы роли

| Делает | НЕ делает |
|--------|-----------|
| Принимает webhook от Discourse, HMAC-проверка | Не отправляет данные обратно в Discourse |
| Polling backfill при downtime / one-time истории | Не заменяет webhook polling'ом постоянно (race condition) |
| Резолв `discourse_user_id` через `club.members` | Не хранит email пользователей клуба (B7.3 PII tax) |
| Пишет события с `account_id IS NULL` если bridge пустой | Не дропает события до резолва (lazy resolve, не блокирующий) |
| Алертит при недоступности Discourse 5+ мин | Не блокирует rewards-projection-worker (decoupled через `domain_event`) |
| Логирует `discourse_user_id`, `event_type`, `category_id` | Не логирует email, имена, тексты постов (PII tax) |

## 5. Артефакты

**Входы:**
- Discourse webhook payload (JSON, HMAC signature)
- Discourse REST API response (paginated)
- ORY API (one-time для backfill bridge)

**Выходы:**
- Строки в `domain_event` (БД #3)
- Строки в `club.members` (bridge) и `club.cursor` (state)
- Строки в `health.connector_status` (БД #8)

## 6. Носители (carriers)

**Реализация:** один из вариантов (решается ArchGate Ф2 WP-296):
- (a) **CF Worker** (TypeScript) — webhook endpoint + cron trigger для polling. Плюсы: edge, дёшево, единый стек с gateway-mcp. Минусы: 30s CPU limit на запрос (для backfill 63K — придётся chunk'ать).
- (b) **Railway worker** (Python/Node) — long-running процесс. Плюсы: нет CPU limit, проще backfill 63K за один проход. Минусы: один сервер (SPOF без healthcheck restart).

**Дефолт-решение (предложение для ArchGate):** **CF Worker для webhook** (real-time, edge) + **Railway/cron worker для backfill** (long-running). Гибрид. Обе части пишут в один и тот же `domain_event`.

## 7. Метрики

- `club_events_ingested_total{event_type, source_path=webhook|polling}` — counter
- `club_events_resolved_total` — те, у кого `account_id IS NOT NULL` (после bridge'а)
- `club_ingest_lag_seconds` — `now() - max(occurred_at)` за окно 5 мин
- `club_bridge_coverage_ratio` — `count(members WHERE ory_identity_id IS NOT NULL) / count(*)`
- `club_webhook_hmac_failures_total` — counter (signal атаки или ротации secret)

## 8. Связи

- **Реализует:** [DP.SC.128](../08-service-clauses/DP.SC.128-club-ingest.md) Ingest активности клуба
- **Использует:** event-gateway паттерн (DP.SC.020), карту Neon (DP.ARCH.004)
- **Питает:** rewards-projection-worker, Метабазу, WP-117 nudge-систему, WP-121 правила баллов
- **WP:** WP-296

## 9. Открытые вопросы (для ArchGate Ф2 WP-296)

1. Bridge-таблица в #3 activity-hub schema `club.*` ИЛИ новая БД `club`?
2. Carrier: только CF Worker, только Railway worker, или гибрид?
3. Webhook secret — где хранить? CF env var (предпочтительно) ИЛИ Railway secret.
4. Кто пишет `club.cursor` при гибриде webhook+polling — конфликт или разделение namespace?
