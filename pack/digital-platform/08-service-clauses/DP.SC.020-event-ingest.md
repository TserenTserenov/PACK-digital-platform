---
id: DP.SC.020
name: Event Ingest (единый приёмник доменных событий)
type: sc
status: draft
layer: L2-Platform
summary: "Единая точка приёма доменных событий обучения от всех источников с идемпотентностью, валидацией и PII-фильтрацией"
consumer: Системы-источники (LMS, бот, MCP, скиллы, aggregator)
created: 2026-04-24
updated: 2026-04-24
related:
  realizes: [WP-253]
  uses: [DP.ARCH.004]
  source: "WP-253 Ф9 MVP-greenfield — IntegrationGate для event-gateway"
---

# DP.SC.020 — Event Ingest (единый приёмник доменных событий)

## Обещание

**Кому:** Системам-источникам доменных событий обучения — LMS Aisystant, aist-bot, MCP-сервисам (knowledge, twin), IWE-скиллам (`/personal-guide-start`, capture-flow), aggregator'у из журнала.

**Зачем:** До этого писатели writer'или в свои БД (aist_bot, LMS systemschool) или в общую без контрактов. Это давало: (а) нарушение OwnerIntegrity (несколько writer'ов одной учётной единицы); (б) race-условия при webhook-петлях; (в) утечку PII в свалочные JSONB.

**Что получит источник:**
- Единая точка приёма `POST /events` с гарантированной идемпотентностью по ключу `(source, external_id)`.
- Валидация payload по JSON Schema, привязанной к паре `(source, event_type)` — запрещённые поля (telegram_id, email, message_text) отклоняются на ingress.
- `202 Accepted` сразу после записи в буфер — источник не блокируется, eventual consistency.
- Асинхронная запись в `learning.domain_event` — читатели (calculate_points, analytics) видят событие в пределах ≤5s в нормальном режиме.

## Критерий приёмки

1. **Идемпотентность:** повторный POST с тем же `(source, external_id)` возвращает 202 без дубля в таблице (проверка: 100 повторов одного события → 1 строка в БД).
2. **Валидация PII:** payload с полем `email`/`telegram_id`/`phone` без schema-whitelist отклоняется с 400 + причиной (проверка: synthetic payload с PII → reject + лог в security-incident).
3. **Latency p95 ≤500ms** для POST /events под нагрузкой 50 rps (Cloudflare Worker + Neon pooled).
4. **Durability (MVP):** при недоступности Neon источник получает 503, делает retry с exponential backoff; идемпотентность (`UNIQUE source, external_id`) гарантирует отсутствие дублей при повторах. **Phase 2 (при кросс-БД проекции или росте нагрузки ×100):** CF Queues поверх event-gateway — события копятся ≥24h, worker дренит после восстановления Neon. Решение MVP: прямая запись без outbox (см. DP.ROADMAP.001-A §ArchGate П1).
5. **Observability:** каждое событие → trace в Langfuse с `source`/`event_type`/`user_id`. Расхождение ingress/egress счётчиков >1% → TG-алерт в #platform-health.

## Архитектура

```
Источники ─── POST /events ──► event-gateway (Cloudflare Worker)
                                        │
                                        ├── JSON Schema validate (per source+event_type)
                                        ├── PII reject (whitelist полей)
                                        ├── Idempotency check (source, external_id)
                                        ▼
                               INSERT learning.domain_event (прямая запись в Neon для MVP)
                                        │
                                        ▼
                               LISTEN/NOTIFY → rewards projection-worker (Python, Railway) → rewards.point_balances

Phase 2 (опционально при росте нагрузки ×100 или кросс-БД проекции):
  event-gateway → CF Queues → consumer-worker → INSERT learning.domain_event
```

## Схема таблицы

```sql
CREATE TABLE learning.domain_event (
  id BIGSERIAL PRIMARY KEY,
  user_id UUID NOT NULL,
  event_type TEXT NOT NULL,      -- 'lesson_completed', 'skill_applied', 'capture_saved', 'slot_logged'
  source TEXT NOT NULL,          -- 'lms-aisystant', 'aist-bot', 'mcp-knowledge', 'skill-personal-guide', 'journal-aggregator'
  external_id TEXT,              -- id в системе-источнике (для идемпотентности)
  payload JSONB NOT NULL,
  occurred_at TIMESTAMPTZ NOT NULL,
  ingested_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE (source, external_id)
);
```

## JSON Schema пример (для source='lms-aisystant', event_type='lesson_completed')

```yaml
required: [user_ory_id, lesson_id, course_id, completed_at]
properties:
  user_ory_id: {type: string, format: uuid}
  lesson_id:   {type: integer}
  course_id:   {type: integer}
  completed_at: {type: string, format: date-time}
additionalProperties: false    # любые дополнительные поля = reject
forbidden_fields: [email, telegram_id, phone, message_text]
```

**Schema location (MVP):** в коде event-gateway (`DS-MCP/event-gateway/src/schemas/*.json`). **Phase 2:** перенос в `reference.event_schemas` (БД #8) при появлении plugin-источников (WP-258, Q3) — там hot-reload без deploy критичен. Решение MVP: в коде (см. DP.ROADMAP.001-A §ArchGate П3).

## Источники (текущие и плановые)

| Source | Event types | Режим доставки |
|--------|-------------|----------------|
| `lms-aisystant` | lesson_completed, qualification_granted, payment_received | Bridge-1 (Cloudflare Worker polling 15 мин — фаза coexistence) → прямой POST (после переделки LMS Димой) |
| `aist-bot` | slot_logged, command_invoked | Inline в bot handler → POST |
| `mcp-knowledge` | knowledge_searched, document_accessed | Inline в MCP tool handler |
| `skill-personal-guide` | skill_invoked, guide_rendered | Inline в skill |
| `journal-aggregator` | (derived events из raw journal) | Отдельный worker (journal → gateway) |

## Режимы отказа

| Сбой | Поведение | Восстановление |
|------|-----------|----------------|
| Neon недоступен (MVP) | Gateway возвращает 503, источник делает retry с exponential backoff | После восстановления Neon источник успешно повторяет; UNIQUE constraint блокирует дубли |
| Neon недоступен (Phase 2, при добавлении CF Queues) | Gateway пишет в CF Queues, возвращает 202 | Consumer-worker дренит очередь после восстановления Neon |
| Gateway недоступен | Источник получает 503/timeout, retry с exponential backoff | После восстановления gateway принимает буферизованные на клиенте события |
| Schema validation fail | 400 + причина + лог в `security.reject_log` | Source-команда исправляет payload, retry; schema-issue эскалируется владельцу Source |
| PII обнаружен | 400 `pii_forbidden` + инцидент в #security | Source-команда убирает PII из payload (обычно: использовать `user_uuid` вместо `telegram_id`) |

## Граница ответственности

| event-gateway делает | Не делает |
|----------------------|-----------|
| Принимает POST /events | Не интерпретирует event_type (домен — это projection-worker) |
| Валидирует payload | Не считает баллы (это rewards projection-worker) |
| Дедуплицирует по `(source, external_id)` | Не хранит состояние пользователя (это Память.Derived) |
| Пишет в learning.domain_event | Не rebuild'ит проекции (это projection-worker по LISTEN/NOTIFY) |

## Related

- **Роль:** DP.ROLE.032 Event Ingester
- **Реализация:** `DS-MCP/event-gateway/` (Cloudflare Worker, создаётся в Ф9.1 WP-253)
- **Карта БД:** DP.ARCH.004 v2.2 §8 — writer `learning.domain_event` = event-gateway (патч v2.3 через WP-228 Ф28 reopen)
- **Родительский РП:** WP-253 Ф9 MVP-greenfield
