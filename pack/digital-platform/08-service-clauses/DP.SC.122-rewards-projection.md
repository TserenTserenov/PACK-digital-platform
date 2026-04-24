---
id: DP.SC.122
name: Rewards Projection (точная проекция баллов по доменным событиям)
type: sc
status: draft
layer: L2-Platform
summary: "Точная идемпотентная проекция из learning.domain_event в rewards.point_balances по reference.reward_rules через LISTEN/NOTIFY"
consumer: Читатели баланса баллов — бот (/balance), gateway-mcp (/twin), Метабаза, будущий UI
created: 2026-04-24
updated: 2026-04-24
related:
  realizes: [WP-253]
  uses: [DP.ARCH.004, DP.SC.020]
  source: "WP-253 Ф9.3 MVP-greenfield — IntegrationGate для projection-worker"
---

# DP.SC.122 — Rewards Projection

## Обещание

**Кому:** читателям баланса баллов пользователей — бот (команда `/balance`), gateway-mcp инструмент `/twin`, Метабаза (BI), будущий Web UI.

**Зачем:** до этого баллы считались в двух местах — legacy `payment-registry.points_*` и экспериментальный скилл-handler. Расхождение между ними = споры «сколько у меня баллов на самом деле». Нужен **единственный источник истины** — пересчёт из событий по опубликованным правилам.

**Что получит потребитель:**
- Запрос `SELECT points FROM rewards.point_balances WHERE account_id = $1` даёт актуальное значение в пределах **≤1 секунды** после записи события в `learning.domain_event`.
- Баллы **всегда** равны сумме начислений по всем сработавшим `reference.reward_rules` для всех событий с этим `account_id` (контрактно — не эвристика, пересчитываемо).
- Повтор одного события **не удваивает** баланс (идемпотентность через cursor).
- Downtime проекции ≤15 мин не теряет события — после рестарта replay с cursor закрывает gap.

## Критерий приёмки

1. **Latency p95 ≤1s** между `INSERT learning.domain_event` и `UPDATE rewards.point_balances` под нагрузкой 10 events/s (LISTEN/NOTIFY + Python asyncpg + Neon pooled).
2. **Идемпотентность:** повтор обработки того же `domain_event.id` не изменяет баланс — UPDATE cursor + UPDATE balance в одной транзакции, cursor-check перед apply.
3. **Gap recovery:** после перезапуска worker читает `rewards.processed_events.last_event_id`, применяет все события с `id > cursor` — 10k events за ≤60s.
4. **Determinism:** при одинаковой истории событий и одинаковых `reward_rules` (valid_from/to неизменны) финальный `point_balances` идентичен независимо от порядка NOTIFY — правила отсортированы по `rule_id`, события — по `id`.
5. **Business invariant:** `CHECK (points >= 0)` не нарушается — при попытке увести в минус транзакция откатывается, cursor не двигается, событие возвращается при replay.
6. **Observability:** каждое применение правила → structured log с `event_id`, `rule_id`, `account_id`, `amount`; расхождение `events_processed` vs `domain_event count` >0.1% за 1h → TG-алерт.

## Архитектура

```
event-gateway (DP.ROLE.032) ─── INSERT learning.domain_event
                                       │
                                       ├─ AFTER INSERT trigger → pg_notify('domain_event_added', id)
                                       ▼
  ┌────────────────────────────────────────────────┐
  │ projection-worker (DP.ROLE.034, Python Railway)│
  │                                                │
  │   on NOTIFY(event_id):                         │
  │     cursor := get_cursor(rewards)              │
  │     if event_id ≤ cursor: skip                 │
  │     event := fetch_event(learning, event_id)   │
  │     apps  := match(event, cached_rules)        │
  │     tx(rewards):                               │
  │       UPDATE point_balances ...                │
  │       UPDATE processed_events SET cursor=event_id
  │                                                │
  │   startup: replay events from cursor           │
  │   loop: refresh rules from reference every 60s │
  └────────────────────────────────────────────────┘
                                       │
                                       ▼
                        rewards.point_balances ← читают бот/gateway/Metabase
```

## Матчинг правил

Правило `reference.reward_rules` применяется если:
1. `rule.trigger_event == event.event_type` (строгое равенство).
2. `rule.match_condition` — `NULL` (unconditional) **ИЛИ** subset-match: каждая пара `(key, value)` из condition присутствует в `event.payload` со строгим равенством (без type coercion).
3. Правило активно: `valid_from <= now() AND (valid_to IS NULL OR valid_to > now())` — проверяется при загрузке правил из БД (не в матчере).
4. Событие имеет `account_id` — иначе некого награждать, правило пропускается.

Несколько правил могут сработать на одно событие — все применяются (суммируются в balance).

## Режимы отказа

| Сбой | Поведение | Восстановление |
|------|-----------|----------------|
| Neon learning недоступен | NOTIFY пропускается, worker retry'ит на следующем NOTIFY | Replay от cursor после восстановления |
| Neon rewards недоступен | Транзакция откатывается, cursor не двигается | Следующий NOTIFY или restart → replay |
| Neon reference недоступен | Используется cached rules (последний успешный fetch) | Через 60s будет попытка refresh |
| Правило уводит в минус | CHECK (points >= 0) блокирует, tx rollback | Ручной разбор (rule design error) — incident |
| Worker crash | Systemd/Railway рестарт, startup replay от cursor | ≤60s gap recovery |
| Потеряна NOTIFY (сеть/замок) | Следующая NOTIFY триггерит catch-up через replay | Автоматическое — cursor-based |

## Граница ответственности

| projection-worker делает | Не делает |
|--------------------------|-----------|
| LISTEN domain_event_added | Не принимает входящие события (это event-gateway, DP.SC.020) |
| Применяет reward_rules → point_balances | Не создаёт правила (это admin через Directus) |
| Обеспечивает idempotency через cursor | Не валидирует payload (это event-gateway) |
| Replay от cursor на старте | Не rebuild'ит balance с нуля (только forward) — rebuild = отдельный CLI `replay --from=0` |
| Атомарная tx (balance + cursor) | Не обрабатывает achievement (Phase 2 — отдельный projection) |
| Cache rules 60s | Не пишет rules (read-only для reference) |

## Related

- **Роль:** [DP.ROLE.034 Rewards Projector](../02-domain-entities/DP.ROLE.034-rewards-projector.md)
- **Писатель событий:** [DP.SC.020 Event Ingest](./DP.SC.020-event-ingest.md) (event-gateway)
- **Реализация:** `DS-IT-systems/rewards-projection-worker/` (Python 3.11+ asyncpg, Railway)
- **Карта БД:** DP.ARCH.004 v2.3 §3.12 (reader `learning.domain_event`, writer `rewards.point_balances`, writer `rewards.processed_events`)
- **Родительский РП:** WP-253 Ф9 MVP-greenfield
