---
id: DP.M.073
name: Pause-before-fix для воркеров с downstream notifications
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-310 Ф14 (R46 controller paused перед запуском stage_evaluator, unpause 18 мая после ручной проверки)
  - captures.md 2026-05-17 session-close-feed (WP-326 close)
related:
  complements:
    - DP.M.067  # split-tx — другой режим разрезания обработки
  applies_to: [WP-310]
---

# DP.M.068: Pause-before-fix для воркеров с downstream notifications

## Определение

Операционный метод для hotfix эволюционно-копящегося воркера (evaluator, projection-worker, любой агрегатор по N сущностям), у которого есть downstream notification controller (бот, рассылающий сообщения по новым переходам/событиям).

Прямой порядок «fix evaluator → controller проснулся → видит N новых событий → разошлёт N notifications за минуту» создаёт каскад false-positive notification'ов пользователям (нудж 17 мая о переходе, который произошёл 14 мая логически).

**Правильный порядок:**

1. `systemctl stop notification-controller.timer` (или эквивалент) **ДО** fix.
2. Применить fix к evaluator → backfill пропущенных events.
3. **Ручная проверка** выборочно: «эти notification'ы действительно надо слать?» Пилот/owner идёт по stage_transitions / projection_events / любой downstream-таблице и подтверждает, что events релевантны для notification.
4. `systemctl start notification-controller.timer` → controller обрабатывает уже-просмотренный backlog.

## Тест применимости

> «Fix воркера может создать в downstream-таблице ≥10 новых записей за короткое время, и каждая запись триггерит user-facing notification?»

- **Да** → pause-before-fix обязателен.
- **Нет** (fix не влияет на downstream объём; либо notification'ы не user-facing) → достаточно прямого fix.

## Применимость

- Evaluator-workers с downstream-нотификациями (WP-310 R46/Аттестатор).
- Projection-workers, обновляющие user-visible dashboard (если есть signal «новое значение → push»).
- Retroactive миграции данных с триггерами, эмитирующими события.
- Любой fix-after-drift где «фикс» создаёт «всплеск» в downstream.

## Контр-pattern

«Fix → запустить всё, потом разберёмся» = массовая рассылка false-positive за минуту → недоверие пользователей к системе. Один false-positive обычно прощается; ≥10 за минуту = инцидент в восприятии.

## Прецедент

WP-310 Ф14 (17 мая): stage_evaluator пропускал cp_assessments → backlog 7 stage_transitions за 3 дня. Без pause R46 controller разослал бы 7 нуджей пилотам за минуту, формально корректных, но воспринимаемых как «бот сошёл с ума». Pause + ручная проверка + unpause утром 18 мая (Пн) → плавная доставка.

## Связи

- Контрастирует с `DP.M.067` (split-tx): M.067 — про разделение commit'а внутри одной операции; M.068 — про управление потоком между независимыми воркерами.
- Дополняет `feedback_alerter_writer_sampling_drift.md` (idle ≠ stuck без backlog-проверки) — здесь «есть backlog, но не значит, что нужно немедленно проинформировать».
