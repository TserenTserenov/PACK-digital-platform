---
id: DP.D.107
type: distinction
title: UI-подтверждение callback'а ≠ подтверждение side-effect'а
status: proposed
trust: provisional
epistemic_stage: B
created: 2026-05-27
source: "session-transcript 2026-05-27 (WP-330 Ф8.2: «нажад и вышло, а потом исчез Тупик»)"
domains: [ui, async, callbacks, verification, telegram-bot]
---

# DP.D.107 UI-подтверждение callback'а ≠ подтверждение side-effect'а

## Различение

**UI-ack:** обработчик callback'а получил управление (исчез spinner, пропала кнопка, проставился чек-марк, ACK отправлен в Telegram/Slack/WhatsApp).
**Side-effect:** бизнес-логика выполнила запись в БД, отправила письмо, начислила баллы, инкрементировала счётчик.

UI-ack подтверждает только первое. Между ACK и DB-commit'ом возможны:

- early-return на existing-record/no-op условии,
- silent except на uniqueness/constraint violation,
- read-only ветка кода, не дошедшая до writer'а.

## Тест

«Один сигнал подтверждает, что код-путь до writer'а реально пройден?» Нет → это UI-ack, не side-effect.

## Прецедент

2026-05-27, WP-330 Ф8.2 (`aist_bot_newarchitecture`):

- Пилот нажал на пилотном боте, inline-кнопка пропала.
- Counter в БД не инкрементировался — `update_progress` был пропущен на existing-record ветке.
- Жалоба: «Я нажал на пилоте, но ничего не произошло.»
- Корень: ACK дошёл (UI-ack ✅), DB-write не произошёл (side-effect ❌). Один сигнал маскировал отсутствие другого.

## Verify-стратегия для пилота/QA при smoke-test'е

1. **UI-сигнал** = обнаружение «callback fired» (необходимое, недостаточное).
2. **DB-query** по бизнес-полю = подтверждение side-effect.
3. **Логи обработчика** = объяснение пройденного пути кода.

Все три сигнала должны совпасть. Один UI-сигнал ≠ другой.

## Применимость

Любые async UI с visual ack:

- Telegram inline keyboards
- WhatsApp interactive messages
- Slack actions
- Web SPA с optimistic update'ами
- Mobile push receipts

## Анти-паттерн

«Кнопка исчезла → работает» → «не воспроизвести» баг-репорты, ложно-зелёные smoke-тесты, потеря side-effect'ов без trace'а.

## Связи

- **Прецедент:** WP-330 Ф8.2, commit b9c14201 (`aist_bot_newarchitecture`)
- **Родственное:** DP.M.196 (UPSERT verify через двойную дельту) — конкретный случай той же verify-логики применённой к UPSERT.
