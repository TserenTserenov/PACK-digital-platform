---
id: DP.SC.101
name: LMS Subscription Webhook (Bridge-2 контракт с LMS Aisystant)
type: sc
status: draft-not-delivered
layer: L2-Platform
summary: "Контракт endpoint'а на стороне LMS Aisystant для приёма подписок от нашего payment-receiver. Артефакт для передачи Диме."
consumer: LMS Aisystant (команда Димы)
provider: IWE payment-receiver
created: 2026-04-24
updated: 2026-04-24
related:
  realizes: [WP-253]
  uses: [DP.SC.020]
  source: "WP-253 Ф9 — Bridge-2 контракт"
handoff:
  to: "Дмитрий Ляшенко (LMS Aisystant)"
  when: "после стабилизации MVP на волонтёрах (≈17 мая)"
  status: "not-delivered-yet"
---

# DP.SC.101 — LMS Subscription Webhook (Bridge-2)

## Статус документа

**Draft, не передан Диме.** Передача планируется **после** стабилизации MVP на волонтёрах (≈17 мая). До этого момента подписки волонтёров живут только в нашем Neon `subscription` — LMS их не видит. Это приемлемо, потому что волонтёры — core team (5 человек), они не оплачивают через LMS в MVP-период.

## Обещание (что мы просим у Димы)

**Кому:** Dimитрий Ляшенко / команда LMS Aisystant.

**Зачем:** Источник истины по подпискам переезжает в наш Neon (`subscription.subscription_grants`). LMS сейчас ведёт собственный `user_subscriptions` для гейта к курсам. Чтобы оба сервиса видели одну картину — нужен **webhook**: мы уведомляем LMS о каждом INSERT/UPDATE `subscription_grants`, LMS зеркалит у себя.

**Что LMS реализует:**

Endpoint, принимающий POST с нашей стороны. Характеристики ниже.

## Характеристики endpoint'а

### URL

```
POST https://lms.aisystant.com/api/webhooks/iwe/subscription-grant
```

Рекомендуем не публичный URL — бросаем за cloudflare с IP allowlist на наш Worker.

### Authentication

```
Authorization: Bearer <IWE_LMS_SERVICE_TOKEN>
Content-Type: application/json
```

`IWE_LMS_SERVICE_TOKEN` — общий секрет, ротируется раз в квартал. Генерим мы, передаём Диме в vault.

### Request body

```json
{
  "idempotency_key": "sub-grant-{uuid}",
  "event_type": "grant.created",        // или "grant.updated", "grant.revoked"
  "source": "iwe-payment-receiver",
  "user": {
    "ory_id": "ory-uuid-...",
    "email": "user@example.com",        // если известен из Ory
    "telegram_id": 123456789              // если известен из бота
  },
  "subscription": {
    "id": "grant-uuid-...",
    "type": "pack_digital_platform",    // или иной тариф из DP.SC.015
    "valid_from": "2026-05-02T00:00:00Z",
    "valid_until": "2026-08-02T00:00:00Z",
    "payment": {
      "external_payment_ref_hash": "sha256(yookassa:yk-abc123)",  // хэш handle, не сам ID
      "payment_system": "yookassa",
      "amount_rub": 2000
    }
  }
}
```

### Response

**Успех:**
```
HTTP 200 OK
Content-Type: application/json

{"status": "ok", "lms_subscription_id": 12345}
```

**Идемпотентность (повтор того же idempotency_key):**
```
HTTP 200 OK
{"status": "duplicate", "lms_subscription_id": 12345}
```

**Ошибки:**
- `HTTP 401` — невалидный token
- `HTTP 400` — malformed payload (с деталями)
- `HTTP 500` — внутренняя ошибка LMS → мы retry до 5 раз с exponential backoff

### Гарантии, которые даём мы

- **At-least-once delivery** — одно событие может прийти 1+ раз. LMS обязан идемпотентно обработать по `idempotency_key`.
- **Ordered per-user** — для одного `user.ory_id` события приходят в порядке `occurred_at` (мы не шлём `grant.revoked` до `grant.created`).
- **Retry budget** — 5 попыток с backoff 5s/30s/5min/30min/6h. После — событие в DLQ + алерт нам.
- **Throughput** — в MVP ≤10 событий/сутки. Можно без оптимизаций.
- **Не передаём payment_credentials.** По DP.ARCH.004 §2 П6.1 `external_payment_id` (handle к транзакции YooKassa/Stars) относится к классу `payment_credentials` (строже PII). В payload передаётся только односторонний хэш `external_payment_ref_hash = sha256(payment_system + ':' + external_payment_id)` — позволяет Диме связать запросы поддержки от пользователя с конкретной транзакцией без передачи самого handle. Сопоставление с YooKassa-кабинетом остаётся на нашей стороне (payment-receiver hashes → external_id mapping в `subscription.payment_audit`).

### Гарантии, которые ждём от LMS

1. Endpoint возвращает 200 **только после** записи в `user_subscriptions` (не fire-and-forget).
2. `idempotency_key` сохраняется ≥30 дней для дедупа.
3. Field `source='iwe-payment-receiver'` не перезаписывается LMS-логикой (позволяет Bridge-1 фильтровать наши же записи).
4. При `grant.revoked` — доступ к курсам закрывается ≤15 мин.

## Альтернативы (если endpoint Диме не подходит)

**Альт-1 (append-only outbox):** Мы пишем события в публичную таблицу `subscription.outbox_for_lms`, Дима pull'ит её cursor'ом по `created_at DESC`. Плюс: нет endpoint'а на стороне LMS. Минус: задержка зависит от pull-частоты Димы.

**Альт-2 (shared table):** Мы даём Диме service-user с `INSERT` на `subscription.subscription_grants` напрямую. Плюс: zero coordination. Минус: нарушает OwnerIntegrity (`subscription` становится с двумя writer'ами).

Рекомендуем основной вариант (webhook). Альт-1 — fallback если Дима занят.

## Вопросы к Диме (к обсуждению при передаче)

1. Какой схемой зеркалить у вас — `user_subscriptions` или отдельная таблица `iwe_subscriptions`?
2. Как обрабатывать overlap с текущими подписками LMS (пользователь купил курс в LMS *и* имеет IWE-подписку)?
3. Нужны ли дополнительные события: `trial.started`, `trial.ended`, `upgrade.applied`?
4. Готов ли LMS поднять endpoint за 5 дней после передачи документа, или нужно дольше?

## Related

- **Обратный Bridge:** Bridge-1 LMS → наш event-gateway (DP.SC.020) — pulls lesson_progress/payment из `systemschool.*`.
- **Event Ingester:** DP.ROLE.032 — использует `source='lms-aisystant'` для маркировки событий от Bridge-1.
- **Целевая таблица (наша):** `subscription.subscription_grants` в БД #4.
- **Родительский РП:** WP-253 Ф9 MVP-greenfield.

## История передачи

| Дата | Событие | Исполнитель |
|------|---------|-------------|
| 2026-04-24 | Draft создан в Pack | Tseren |
| ≈2026-05-17 | Передача Диме (после MVP smoke) | Tseren |
| TBD | LMS реализация endpoint'а | Дима |
| TBD | Интеграционный smoke-test | Совместно |
