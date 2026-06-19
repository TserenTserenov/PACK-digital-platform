---
id: DP.SC.182
name: Учётчик следов (trace-accountant)
type: sc
status: draft
layer: L2-Platform
summary: "Принимает сырые следы от сенсорных адаптеров, проверяет consent, нормализует, маршрутизирует в домы знания по route_catalog, управляет stub-буфером offline+restrictive, ведёт reconciler-отчёт. Единственный authorized writer в learning.domain_event."
consumer: Навигатор/Портной (ЦД, развитие человека), projection-worker (баллы), R15 Экстрактор (pending_review → Pack), пилот (reconciler-отчёт)
created: 2026-06-19
related:
  realizes: [DP.ROLE.077]
  related_to: [DP.SC.135, DP.SC.181, WP-427]
  see_also: [WP-423, WP-316, WP-417]
wp: WP-427
---

# [DP.SC.182] Учётчик следов (trace-accountant)

> # see DP.SC.182, DP.ROLE.077

<!--
  Зеркальная пара к WP-417 (доставка → пользователь).
  Здесь поток обратный: след ОТ пользователя через много сенсоров в разные дома.
  Вариант A ArchGate Ф3b WP-427: новая роль + свой транспорт (не поверх event-gateway).
  Источник: peer-сессия 2026-06-19-21 (Claude+Claude).
-->

## Проблема, которую закрывает

IWE ловит лишь малую часть следов пользователя. Ценные интерфейсы (VS Code чат, claude.ai) не записываются вообще. Часть следов в боте (заметки, рефлексия, фидбек) пишется в стол. Нет единой модели: куда что попадает, под чьим consent, в какой дом, ради какого потребителя. Следствие: projection-worker не начисляет баллы за действия, которые не дошли до `learning.domain_event`; Навигатор не видит следов из половины интерфейсов.

Зеркало различения [[Сенсор захвата ≠ дом знания]] из distinctions.md.

## Правило (инвариант)

- **И1 — единый read-SoT.** Все статусы захвата отражены в логическом журнале `learning.domain_event`. Статус `stored` означает: след в доме И в журнале.
- **И2 — партиционированный единственный writer.** Облачный инстанс — единственный writer в `learning.domain_event` и `trace_stubs`. Локальный инстанс — единственный writer в `local-vault/staging/`. Два инстанса не пишут в дома друг друга.
- **И2 migration scope.** И2 enforced after cut-over. До cut-over legacy-writer'ы допустимы time-bounded на период Ф4.
- **Consent-guard = write-path инвариант.** Ни одна запись в дом не происходит без consent-проверки. Это не транспортный gate, а условие операции Учётчика.
- **Fail-open при infra-сбое consent-хранилища.** При недоступности consent-хранилища след получает статус `consent_unverified` (не `stored`, не потеря). Downstream-потребители могут отфильтровать/аудитировать.
- **Адаптер = fire-and-forget.** Сенсорный адаптер не несёт ответственности за pending state. После передачи следа Учётчику работа адаптера завершена.
- **Граница с event-gateway.** event-gateway = транспортный слой (JWT-verify + prefix routing). Учётчик = семантический слой (consent + normalization + routing + TTL). Два разных уровня. event-gateway маршрутизирует к Учётчику по prefix как к любому MCP-сервису.

## Обещание

**Триггер:** сенсорный адаптер передал сырой след.

**Входы:**
- Сырой след `{sensor_id, content, timestamp}` от адаптера
- Политика consent пользователя (additive / restrictive)
- Статус соединения (online / offline)

**Выходы:**

| Статус | Значение |
|--------|---------|
| `stored` | Запись в `learning.domain_event` + целевой дом |
| `pending_review` | Предложение пилоту через `/apply-captures` (R15) |
| `pending_consent` | Stub в `trace_stubs`; локальный инстанс заберёт при reconnect |
| `consent_unverified` | Infra-сбой consent-хранилища; записано без проверки, аудитируемо |
| `expired_consent_block` | TTL stub истёк (72h default); reconciler-отчёт пилоту |
| `rejected` | Явный отказ пользователя |
| `orphaned` | Reconciler нашёл след в сенсоре, которого нет в `domain_event` |
| `duplicate_skipped` | Дублирует запись в окне `dedup_window` (из route_catalog) |

**Время отклика:**
- Синхронный путь (облачные сенсоры): ≤500ms
- Асинхронный staging (offline+restrictive): ≤72h до истечения TTL

**Инварианты:** И1, И2 (см. выше). PII-guard B7.3 — ДО записи.

**Режим отказа:**
- infra-сбой consent-хранилища → `consent_unverified` + громкий алёрт (fail-open, [[Явный policy-deny ≠ infra-сбой стража]])
- infra-сбой `learning.domain_event` → `orphaned` в локальный fallback-log
- TTL stub истёк без reconnect → `expired_consent_block` + reconciler-отчёт пилоту (24h и 4h предупреждения)

## Сценарии использования

**Сц.1 — Бот-заметка (первый пилот Ф4):**
Пользователь пишет `/note` в Telegram-боте. Адаптер отдаёт след. Consent additive → Учётчик нормализует тип `note_created`, маршрутизирует по route_catalog: `PACK-personal/memory/` + `learning.domain_event`. Projection-worker реагирует на `note_created` → баллы.

**Сц.2 — VS Code сессия (оффлайн, restrictive):**
Stop-хук Claude Code записывает дайджест сессии в inbox/captures/. Локальный адаптер передаёт Учётчику. Consent restrictive + offline → Учётчик (облачный) создаёт stub в `trace_stubs`, возвращает `pending_consent`. Локальный инстанс при reconnect пулит stubs → пишет в `local-vault/staging/`. При согласии пользователя → `pending_review` → R15 Экстрактор.

**Сц.3 — Рефлексия (w_reflection):**
Пользователь завершает `/w_reflection`. Учётчик: тип `reflection_submitted`, consent additive → `learning.domain_event` + `learning.w_reflections`. ЦД-читатель Навигатор использует для оценки ступени. Projection-worker начисляет баллы.

**Сц.4 — Клуб (edge case, уже захвачен WP-296):**
Webhook клуба пишет в `learning.club.*` напрямую (legacy writer). Учётчик проверяет dupe-guard: `(user_id, event_type=club_post, dedup_window=300s)`. При совпадении → `duplicate_skipped`. После cut-over WP-296-канал переходит через Учётчика (И2).

## Конфигурация (route_catalog.yaml, фрагмент)

```yaml
routes:
  - event_type: note_created
    sensor_id: bot_note
    target_home: PACK-personal/memory/
    dedup_window: 60      # секунды
    consent: additive
  - event_type: session_digest
    sensor_id: vscode_session
    target_home: inbox/captures/
    dedup_window: 86400   # 24 часа
    consent: restrictive
  - event_type: weekly_reflection
    sensor_id: bot_reflection
    target_home: learning.w_reflections
    dedup_window: 604800  # 7 дней
    consent: additive
  - event_type: club_post
    sensor_id: club_webhook
    target_home: learning.club.*
    dedup_window: 300     # 5 минут
    consent: additive
```

`dedup_window` = конфигурируемый параметр per event_type, не константа кода.

## Stub-протокол (pending_consent handover)

1. Облачный Учётчик при `restrictive+offline` → INSERT в `trace_stubs`: `{trace_id, user_id, sensor_id, event_type, status: pending_consent, expires: now+TTL, payload_ref}`. Возвращает `accepted_pending` адаптеру.
2. Локальный инстанс при reconnect → `GET /pending?user_id=X&status=pending_consent` → скачивает payload_ref → INSERT в `local-vault/staging/` → PATCH `stub.status = claimed`.
3. Облачный reconciler → сканирует `trace_stubs` WHERE `expires < now` → UPDATE `status = expired_consent_block` → reconciler-отчёт пилоту.

TTL default: 72h. Max: 168h. Уведомление за 24h и 4h до истечения.
