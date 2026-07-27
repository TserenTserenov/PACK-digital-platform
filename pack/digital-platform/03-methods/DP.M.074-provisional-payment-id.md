---
id: DP.M.074
name: Provisional payment_id для late-binding payment APIs
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-327 (реализация рассрочки на TG Stars; commit 8778c004 + последующие)
  - captures.md 2026-05-17 session-close-feed (WP-326 close)
related:
  complements:
    - DP.M.067-split-transaction-late-webhook  # M.067 — что делать в момент webhook'а; M.069 — что делать до webhook'а
    - feedback_upsert_vs_update_for_closed_events.md  # UPDATE для closed events с бедным payload
  applies_to: [WP-327]
---

# DP.M.074: Provisional payment_id для late-binding payment APIs

## Определение

Метод для payment-провайдеров, которые **не возвращают payment_id синхронно** при создании инвойса/сессии — настоящий id приходит только в webhook **после** успешной оплаты.

Большинство payment-провайдеров (ЮКасса, Stripe, PayPal) дают payment_id или session_id в ответе на создание инвойса. Этот id используется как идемпотентный ключ во всех downstream операциях (резервирование, проверки дублей, webhook-matching). Late-binding APIs (TG Stars, Apple IAP, Google IAP) ломают этот контракт: идентификатор существует только постфактум.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость резервирования (сразу после создания инвойса, до webhook) ↔ надёжность идентификации факта оплаты | `provisional_payment_id` закрывает окно гонки между «пользователь подтвердил» и «webhook прилетел», но это значит резервировать балл/слот для платежа, который webhook ещё не подтвердил |
| Простота единого идентификатора через весь flow ↔ хрупкость связки provisional↔real через `invoice_payload` | Шаг 3 (`UPDATE ... SET real_payment_id = $1 WHERE provisional_payment_id = $2`) работает только пока провайдер не обрежет и не потеряет custom-поле payload; если связка рвётся, отката на «просто real_payment_id» для downstream-операций уже нет |

## Решение

1. **До инвойса:** приложение генерирует `provisional_payment_id = uuid4()`.
2. **При создании инвойса:** `provisional_payment_id` передаётся в `invoice_payload` (или эквивалент: metadata, custom_field). Используется немедленно как идемпотентный ключ для резервирования (балл, слот, контракт).
3. **Webhook прибывает с настоящим payment_id + invoice_payload:** приложение link'ает через payload (`UPDATE ... SET real_payment_id = $1 WHERE provisional_payment_id = $2`).
4. **Все downstream операции** (refund, refund-cascade, reconciliation) — через `provisional_payment_id` (стабильный) с возможностью fallback на `real_payment_id` для внешних запросов в провайдера.

## Тест применимости

> «Возвращает ли провайдер payment_id синхронно в ответе на create-invoice?»

- **Нет** (late-binding: TG Stars, Apple IAP, Google IAP) → provisional_payment_id обязателен.
- **Да** → достаточно настоящего id, provisional не нужен.

## Контр-pattern

«Подождём webhook'а, потом запишем reservation» = окно гонки между подтверждением пользователем и webhook'ом, в котором другие операции не видят занятых баллов/слотов. Симптом — double-spend, double-reservation, отрицательные балансы. Применять метод именно `до` инвойса.

## Применимость

- TG Stars (WP-327 — прецедент)
- Apple In-App Purchase (receipt прибывает только при verify)
- Google Play Billing (тот же класс)
- Любой in-app provider с deferred id
- WebHook-only payment flows без synchronous response

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Генерация id перетягивает внимание от связывания | Шаг 1 (`uuid4()`) простой и приятный, поэтому фокус задерживается на нём, а шаг 3 (link через `invoice_payload` matching) — самое хрупкое место метода — недооценивается: реальная проверка «переживёт ли payload конкретного провайдера» откладывается до продакшн-инцидента |
| Happy-path webhook затмевает downstream-операции | Внимание тянется к моменту «webhook прилетел, id связаны» как к завершению задачи, но не доходит до шага 4 — refund/refund-cascade/reconciliation тоже обязаны работать через `provisional_payment_id`; их часто пишут заново через `real_payment_id`, тихо теряя стабильность идентификатора |

## Связи

- Дополняет `DP.M.067` (split-transaction late-webhook): M.067 — внутри обработки webhook'а; M.069 — до начала flow, чтобы webhook вообще мог стартовать без race.
- Дополняет `feedback_upsert_vs_update_for_closed_events.md`: closed event с бедным payload — `UPDATE` через provisional id, не UPSERT.
- Контрастирует с классическим Stripe-flow: там `payment_intent.id` синхронный — provisional не нужен.

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
