---
id: DP.M.072
name: Split-transaction для late-webhook с CHECK constraint
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-327 Quick Close commit 8778c004 (TG Stars confirm_burn разделён на Tx1+Tx2)
  - captures.md 2026-05-17 session-close-feed (WP-326 close)
related:
  complements: [DP.M.060]  # Атомарные ВДВ-шаги — split-tx как способ удержать атомарность шагов webhook'а
  applies_to: [WP-327]
---

# DP.M.072: Split-transaction для late-webhook с CHECK constraint

## Определение

Метод для payment-flow webhook'ов, где доставка события может опоздать настолько, что связанное состояние (баланс баллов, остаток слотов) изменено иными операциями. Монолитная транзакция в этом окне падает на CHECK constraint, провайдер ретраит до timeout, очередь забивается.

Решение: разделить commit на два этапа.

| Tx | Что делает | Гарантия |
|----|-----------|----------|
| **Tx1** | INSERT/UPDATE статуса «факт получения» (`status='confirmed'`, payment_id, метка времени). Коммит безусловный. | Webhook получает HTTP 200, провайдер прекращает ретраи |
| **Tx2** | UPDATE зависимого состояния (point_balances, reservation, slots) с `try/except CheckViolationError`. При исключении — `post_event('admin_alert', ...)` через event-gateway, fail не пробрасывается наружу. | Неконсистентность фиксируется как алёрт для разбора, не как пропадающий webhook |

## Тест применимости

> «Может ли событие webhook'а быть получено в окне, где связанное состояние уже изменено иными операциями (балансы, остатки, лимиты)?»

- **Да** → split-transaction обязателен.
- **Нет** (моментальные платежи без out-of-band взаимодействий) → достаточно монолитной транзакции.

## Применимость

- TG Stars (WP-327)
- ЮКасса, Stripe, любой webhook-based payment provider
- Идемпотентные эмиттеры с CHECK/EXCLUDE constraint на стороне БД
- in-app purchases Apple/Google (late-binding receipt)

## Контр-pattern

«Всё в одной транзакции для атомарности». Атомарность нужна, но не за счёт потери webhook'а — провайдер не отличает «бизнес-фейл» от «временный fail», ретраит обоих → race + дубли + забитая очередь.

## Связи

- Дополняет `memory/feedback_upsert_vs_update_for_closed_events.md` (closed events — UPDATE, не UPSERT)
- Дополняет `DP.M.060` (атомарные ВДВ-шаги — split-tx удерживает атомарность Tx1 как «факт получения»)
