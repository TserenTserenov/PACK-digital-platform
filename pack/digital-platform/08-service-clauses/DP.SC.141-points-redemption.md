---
id: DP.SC.141
name: Зачёт баллов в оплату
type: sc
status: draft
layer: L2-Platform
summary: "Канал «Баллы» в Billing Module: участник применяет накопленные баллы как скидку к оплате сервиса (резерв-перед-оплатой, двухфазный коммит)"
consumer: Участник (платит баллами), Платёжный шлюз (видит сумму со скидкой), Бухгалтер (видит зачёт в audit-trail)
created: 2026-05-17
related:
  realizes: [DP.ECON.001]
  extends: [DP.SC.105, DP.SC.112]
  source: "WP-327 Ф0 + Ф1"
---

# [DP.SC.141] Зачёт баллов в оплату

## Обещание

**Кому:** Участник с накопленными баллами на платформе, оплачивающий семинар / резидентуру / подписку через ЮКассу или TG Stars.

**Зачем:** Реализовать обещание [DP.SC.105 «Экономика вклада»](DP.SC.105-reputation-economy.md) — «баллы можно потратить на сервисы». Закрыть разрыв между баллами как накопление и баллами как валюта.

**Терминология (DP.ECON.001 §1.1):**
- **Баллы** = earned_total, геймификация, никогда не убывают. Видны в лидерборде.
- **Бонусы** = `min(point_balances.points, Σ(active_days_at_qual_i × daily_cap_i))` — доступная скидка с учётом истории квалификации. Тратятся при оплате.
- **Ступень** = cp-профиль квалификации МИМ (определяет daily_cap).

**Что получит:**
- При чекауте сервиса видит сумму доступных **бонусов**: `min(баланс_баллов, Σ(active_days_at_qual_i × cap_i)) × курс`, где курс = `1 бонус ≈ 0.875 ₽` (1¢ × 87.5 ₽/$, текущий курс ЦБ; владелец курса — DP.ECON.001 §6)
- Применяет скидку — **бонусы** резервируются в `redeemed_events(status='reserved')`, ЮКасса видит уменьшенную сумму
- После успешной оплаты (`payment.succeeded` webhook) — резерв подтверждается (`status='confirmed'`), **бонусный баланс** уменьшается (баллы-earned_total остаются неизменными)
- При отмене (`payment.canceled` или timeout 30 мин) — резерв откатывается (`status='rolled_back'`), бонусный баланс восстанавливается
- В истории видит каждое списание **бонусов** с привязкой к payment_id

**Критерий приёмки:**
1. Пилот когорты-1 при чекауте семинара видит сумму скидки из баллов и может её применить
2. После оплаты копилка уменьшается на потраченное; история сохраняется в `rewards.redeemed_events`
3. Двухфазный коммит работает: reserve → confirm (succeeded) ИЛИ reserve → rollback (canceled / timeout 30 мин)
4. Потолок по квалификации работает по 8 степеням МИМ (Ученик ×1.0 → Общественный деятель ×5.0); fallback на «ученик» для NULL или уровней 9-11
5. Идемпотентность по `payment_id` — повторный webhook не дублирует списание

## Сценарии использования (минимум 3, для IntegrationGate)

### Сценарий 1: Оплата семинара рублями со скидкой баллами

| # | Шаг | Кто | Сервис |
|---|-----|-----|--------|
| 1 | Открывает витрину семинаров (`/showcase`), выбирает семинар (цена 5000 ₽) | Участник | Бот |
| 2 | Видит: «Доступно: 12 ₽ скидки из 1400 баллов (потолок: Ученик ×1.0 = 100 баллов/день)» | Участник | Burn-эмиттер: SELECT доступная скидка |
| 3 | Нажимает «Применить» | Участник | Бот |
| 4 | `reserve_burn(account_id, payment_id_predicted, 100)` → INSERT redeemed_events(status='reserved') | Burn-эмиттер | Neon `rewards` |
| 5 | `YooKassa.create_payment(amount=4988 ₽, metadata={payment_id, points_applied: 100, account_id})` | Бот | ЮКасса |
| 6 | Участник оплачивает 4988 ₽ через ЮКассу | Участник | ЮКасса |
| 7 | Webhook `payment.succeeded` → `confirm_burn(payment_id)` → UPDATE redeemed_events(status='confirmed') + emit event `points_redeemed` в `learning.domain_event` | Burn-эмиттер | Neon |
| 8 | Projection-worker видит `points_redeemed` → UPDATE `point_balances.balance -= 100` | DP.ROLE.034 | Neon |

### Сценарий 2: Полная оплата баллами (100% скидка)

| # | Шаг | Кто | Сервис |
|---|-----|-----|--------|
| 1 | Участник со ст. Реформатор ×4.0 (потолок 700 баллов/день, копилка 6857 баллов) выбирает урок 100 ₽ | Участник | Бот |
| 2 | Видит: «Доступно: 100 ₽ скидки из 114 баллов» (`114 × 0.875 = 100 ₽`) | Участник | Burn-эмиттер |
| 3 | Применяет полную скидку | Участник | Бот |
| 4 | Генерируется `payment_id = "zero_" + uuid4()` (нет внешнего платежа). `reserve_burn` 114 баллов с `payment_source='zero_payment'` | Burn-эмиттер | Neon |
| 5 | НЕ создаётся ЮКасса-платёж (amount = 0). Сразу `confirm_burn(payment_id)` + bot выдаёт доступ. | Бот | — |

### Сценарий 3: Отмена оплаты (rollback резерва)

| # | Шаг | Кто | Сервис |
|---|-----|-----|--------|
| 1 | Резерв создан (Сценарий 1, шаги 1-5) | — | — |
| 2 | Участник закрыл окно ЮКассы без оплаты | Участник | — |
| 3 | Через 30 мин cron `rollback_expired_reservations` находит `status='reserved' AND reserved_at < now() - interval '30 min'` | Burn-эмиттер | Neon |
| 4 | UPDATE redeemed_events(status='rolled_back') + emit reverse event | Burn-эмиттер | Neon |
| 5 | Projection-worker видит reverse → восстанавливает баланс | DP.ROLE.034 | Neon |

**Альтернатива:** webhook `payment.canceled` от ЮКассы → immediate rollback (вместо ожидания 30 мин).

### Сценарий 4: Оплата TG Stars (provisional ID pattern)

> TG Stars не выдаёт `payment_id` ДО оплаты — `telegram_payment_charge_id` доступен только в handler'е `successful_payment`. Поэтому для Stars используется provisional-ID (=`invoice_payload`) с UPDATE при received.

| # | Шаг | Кто | Сервис |
|---|-----|-----|--------|
| 1 | Участник выбирает семинар (цена 100 ⭐), решает применить баллы | Участник | Бот |
| 2 | Видит: «Доступно: 12 ⭐ скидки из 140 баллов» | Участник | Burn-эмиттер |
| 3 | Применяет — бот генерирует `payload = "burn_" + uuid4()` (provisional ID) и вызывает `reserve_burn(account_id, payment_id=payload, points=140, payment_source='tg_stars')` | Burn-эмиттер | Neon |
| 4 | Бот отправляет `send_invoice(prices=88 ⭐, invoice_payload=payload)` | Бот | TG Payments |
| 5 | Участник оплачивает 88 ⭐ | Участник | TG |
| 6 | Handler `successful_payment` получает `payment.telegram_payment_charge_id`. Вызывает `confirm_burn_with_charge_id(provisional_id=payload, charge_id=...)` — обновляет `payment_id` в redeemed_events на charge_id, статус → 'confirmed' | Burn-эмиттер | Neon |

**Особенность:** PK в redeemed_events может меняться (provisional_id → charge_id). Решение: `payment_id` остаётся `payload` (UUID), а `charge_id` идёт в `metadata` jsonb для аудита. Это сохраняет PK-инвариант и одновременно связь с TG payment.

### Сценарий 5: Late webhook after rollback (dead-letter)

> Edge case: оплата прошла, но webhook задержался >30 мин → cron откатил резерв. Затем ЮКасса повторила webhook. Деньги списаны, баллы вернулись пилоту = unfair gain. Требует ручного разрешения.

| # | Шаг | Кто | Сервис |
|---|-----|-----|--------|
| 1 | reserve_burn (status='reserved') | Burn-эмиттер | Neon |
| 2 | Пилот оплачивает в ЮКассе, но webhook задерживается (сеть/timeout) | Участник | ЮКасса |
| 3 | Cron `rollback_expired_reservations` через 30 мин: status → 'rolled_back' | Burn-эмиттер | Neon |
| 4 | ЮКасса повторяет `payment.succeeded` (через час) | ЮКасса | webhook |
| 5 | `confirm_burn` обнаруживает `status='rolled_back'` → НЕ confirm. Эмитирует event `points_redeem_late_webhook` через event-gateway → alert admin (Telegram канал) | Burn-эмиттер | event-gateway |
| 6 | Admin решает: либо ручной refund ЮКассы (вернуть деньги пилоту), либо ручной burn (списать баллы как полагается) | Admin через Directus | Manual SQL audit-logged |

**Защита:** Frequency cron каждые 5 мин (не 30) снижает риск, но не устраняет. Стандартный SLA ЮКассы — webhook в течение 30 мин, выбран соответствующий timeout. При учащении late-webhook'ов (>1% от total) — увеличить таймаут до 60 мин.

## Реализующая роль

**DP.ROLE.051 — Burn-эмиттер баллов** ([`DP.ROLE.051-points-redeemer.md`](../02-domain-entities/DP.ROLE.051-points-redeemer.md)).

Реализация: код в `aist_bot_newarchitecture/db/queries/redeem.py` + точки интеграции в `handlers/workshop.py`, `handlers/showcase.py`, `oauth_server.py`.

## Реализация (точки интеграции в коде)

| Файл | Что делать | Точка |
|------|-----------|-------|
| `db/queries/redeem.py` (NEW) | Skeleton: `available_discount()`, `reserve_burn()`, `confirm_burn()`, `rollback_burn()` | новый модуль |
| `handlers/workshop.py:160-180` | Перед `yk.create_payment` — UI «применить баллы?» → `reserve_burn` | extend pay_rub |
| `handlers/showcase.py:330-360` | Аналогично для seminar.price_rub | extend pay_rub |
| `oauth_server.py:1003-1008` | После `process_*_yookassa_webhook` (succeeded) → `confirm_burn(payment_id)` | extend webhook handler |
| `core/scheduler.py` | Cron `rollback_expired_reservations()` каждые 5 мин | новая job |
| `neon-migrations/mvp/226-rewards-redeemed-events.sql` (NEW) | SQL миграция `rewards.redeemed_events` + extension `reward_rules` для `points_redeemed` event_type | новая миграция |

## Связь с другими обещаниями

- **Реализует:** [DP.SC.105](DP.SC.105-reputation-economy.md) (Экономика вклада — конвертация баллов в доступ)
- **Расширяет:** [DP.SC.112](DP.SC.112-subscription-billing.md) (канал «Баллы» в Billing Module)
- **Потребляет:** [DP.SC.122](DP.SC.122-rewards-projection.md) (проекция в `point_balances`), DP.ECON.001 §3.2 (множители квалификации), DP.ECON.001 §6 (курс)
- **Координация:** WP-246 владеет payment-стороной (yookassa create_payment + payment_received). WP-327 использует payment_id как idempotency ключ для burn-операций.

## ArchGate-Б Security Gate (B7.3 чеклист)

> Баллы как payment_credential proxy → PII/финтех scope. Чеклист обязателен ДО SQL-миграции.

| # | Вопрос | Ответ |
|---|--------|-------|
| Б.1 | Какие PII обрабатываются в burn-flow? | `account_id` (Ory UUID) — НЕ PII по GDPR (псевдоним). `telegram_id` — НЕ хранится в redeemed_events. Сумма списания — финансовые данные, требуют audit-trail. |
| Б.2 | Логирование PII? | Запрещено: log только `account_id[:8]` + `payment_id`. НЕ логировать full UUID, email, telegram_handle. |
| Б.3 | Encryption at rest? | Neon TLS-encrypted. Postgres-уровневое шифрование колонок НЕ требуется (нет PAN/CVV). |
| Б.4 | Access control? | RLS на `rewards.redeemed_events`: read — все service roles; write — только `points_redeemer` role (новая). Admin через Directus — read-only. |
| Б.5 | Audit-trail? | INSERT-only ledger (не UPDATE существующих записей за исключением status). Все статусные переходы фиксируются в `state_history` jsonb. Retention 7 лет (по WP-237). |
| Б.6 | Защита от race condition (двойное списание)? | UNIQUE constraint на `payment_id`. `INSERT ... ON CONFLICT DO NOTHING`. Idempotency garanteed. |
| Б.7 | Защита от negative balance? | CHECK `point_balances.balance >= 0`. `reserve_burn` проверяет `available >= requested` ДО insert. Race protected SELECT FOR UPDATE на point_balances. |
| Б.8 | Защита от replay-атаки на webhook? | IP-whitelist ЮКассы (текущий механизм в `YooKassaClient.verify_notification`). Дополнительно: `payment.succeeded` идемпотентен по `payment_id`. |

**Вердикт ArchGate-Б:** ✅ Готов к реализации. Pre-condition: миграция 226 включает RLS-политики + CHECK constraints.

## Калибровка (для Этапа 4)

**Эталонный сценарий** для smoke на типовом пилоте:
- Пилот: Ученик (ст. 1, ×1.0, daily_cap 100)
- Активность: 10ч/нед обучения за последние 4 недели (~28 активных дней)
- Баллы накоплено: ~1400
- Бонусы: `min(1400, 28 дней × 100) = min(1400, 2800) = 1400` бонусов
- Покупка: семинар 5000 ₽
- Ожидаемая скидка: `1400 × 0.875 = 1225 ₽` (при Ученике cap = 100/день, но 28 × 100 = 2800 > накопленных 1400 → ограничение по баллам)

**Эталонный сценарий** для пилота высокой степени:
- Пилот: Реформатор (ст. 7, ×4.0, daily_cap 700)
- Активность: 20ч/нед × 4 нед = ~28 активных дней
- Баллы накоплено: ~22 400
- Бонусы: `min(22400, 28 × 700) = min(22400, 19600) = 19600` бонусов
- Покупка: семинар 5000 ₽
- Ожидаемая скидка: `min(19600, 5000 / 0.875) × 0.875 = 5000 ₽` (100% покрытие)

> Примечание: калибровочный расчёт выше использует упрощённую формулу (один уровень квалификации за весь период). Реальная формула суммирует по периодам: `Σ(days_at_qual_i × cap_i)` из `applied_events.qualification_snapshot`.

**Стоп-критерий:** если payout >30% подписки на типовом пилоте — pause + Дмитрий (от VR-review subagent).

## Связь с DP.ECON.001 §6

DP.ECON.001 §6 «Конвертация: баллы → скидки» декларирует курс `1 балл = $0.01`. WP-327 реализует это как `0.875 ₽/балл` (на момент создания SC — нужна актуализация при изменении курса USD/RUB).

**TODO для DP.ECON.001:** §6 нужно обновить с явным механизмом курса (источник: курс ЦБ на момент списания? фиксированный курс? кто writer?). Это **отдельный РП** (Pack-update, ~1-2h, ПОСЛЕ WP-327).
