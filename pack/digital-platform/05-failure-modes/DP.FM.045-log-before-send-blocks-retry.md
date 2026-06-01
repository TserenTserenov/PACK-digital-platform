---
id: DP.FM.045
name: "log-after-success violation: idempotency-log записан ДО side-effect → retry невозможен"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: delivery
severity: critical
valid_from: 2026-05-30
related:
  remediated_by: [DP.M.086]
  see_also: [DP.FM.099]
tags: [idempotency, notification, delivery, at-least-once, silent-drop, retry-blocked]
source: "WP-330 peer-session 2026-05-29-29 review-01 Critical (handlers/marathon.py marathon_practice flow)"
schema_version: 1
---

# DP.FM.045 — log-after-success violation: idempotency-log записан ДО side-effect → retry невозможен

## Описание

Class-bug в notification/delivery-пайплайнах, где write в idempotency-log и сам side-effect (отправка сообщения, HTTP-запрос, push) реализованы в неправильном порядке: сначала `try_insert_notification(log)`, затем `send_message()`. Если `send_message` падает (network error, downstream 5xx, rate limit) — log уже зафиксировал «отправлено», и следующая попытка retry видит UNIQUE conflict → считает «уже доставлено» → silent drop.

Failure mode асимметричный: пользователь не получает уведомление, но monitoring/dashboard зелёные («one row inserted, treated as success»). Обнаруживается только через жалобу пользователя или ручной reconcile.

## Симптом

- `notification_log` / `idempotency_keys` показывает row, но получатель утверждает «не приходило».
- Retry-механизм (background worker, cron) на повторном tick фиксирует «already sent», skip.
- Логи `send_message` показывают exception в окне между insert log и send.
- Нет alerting, потому что row в log = success-сигнал.

## Механизм

1. Дизайн «сначала пометь, потом сделай» (precaution against concurrent double-send).
2. Реализация: `INSERT INTO notification_log(...) ON CONFLICT DO NOTHING; send_message(...)`.
3. На rare transient failure `send_message` бросает exception → транзакция INSERT уже committed (или autocommit).
4. Retry: `INSERT INTO notification_log(...) ON CONFLICT DO NOTHING` → `0 rows affected` → код интерпретирует как «уже доставлено».
5. Side-effect не происходит, monitoring видит «one row inserted» = success.

## Тест применимости

«Есть ли в коде паттерн `try_insert(log_key); side_effect()` без compensating rollback log при failure side-effect?»
- **Да** → red flag, переставить порядок (log-after-success pattern, DP.M.086) или выйти на двухфазный outbox.
- **Нет** → проверь, что компенсирующий rollback атомарен с try/catch вокруг side-effect.

## Remediation

См. DP.M.086 (notification_log как cheap idempotency state) — каноническая последовательность: `(was_notification_sent?) → send_message() → only-on-success try_insert_notification()`. Альтернатива при concurrent-race — outbox pattern с двухфазным `scheduled → sent` статусом.

## Сигналы

- Pre-commit grep правило: «строки `try_insert.*log` НЕ должны предшествовать `send_message`/`http.post`/`publish` без try/except и rollback».
- Code review red flag: PR добавляет idempotency-log → требуется test на failed-send scenario (mock-send raises → log должен быть пустым ИЛИ помечен `failed`).

## Антипод

Prepay-pattern в финансовых транзакциях: списать ДО доставки (риск-модель «доставка вернёт деньги обратно»). У уведомлений другая риск-модель: «не доставлено» дороже «доставлено дважды» (для большинства типов нотификаций), поэтому log-after-success предпочтительнее log-before-send.

## Связи

- **Remediated by:** DP.M.086 (notification_log как cheap idempotency state)
- **See also:** DP.FM.099 (notify-subscription-tied-to-connection — другой класс silent-drop)
- **Source:** WP-330 peer-session 2026-05-29-29 review-01 Critical fix (handlers/marathon.py:_process_marathon_queue marathon_practice branch)
