---
id: DP.ROLE.046
name: IWE Stage Controller
type: role-description
status: draft
valid_from: 2026-05-17
summary: "Ежедневный фоновый контролёр-диспетчер двумерной стадии мастерства IWE (cp.iwe × cp.cre): обходит опт-инов, сравнивает фактический профиль с маркером связи со ступенью Ученика (FORM.089 §6.3), отправляет TG-нудж для простых шагов или enqueue render для сложного материала."
related:
  specializes: [U.RoleAssignment]
  component_of: [DP.ROLE.001]
  realizes: [DP.SC.139]
  uses:
    - DP.ROLE.044   # Notification Dispatcher — транспорт TG
    - DP.ROLE.041   # Аттестатор — источник learning.stage_transitions
    - DP.ROLE.042   # Диагност — источник learning.cp_assessments
    - learning.nudge_journal  # cool-down idempotency (WP-326)
    - learning.guide_render_queue  # enqueue для Портного
    - learning.domain_event  # event log
  downstream_consumers:
    - Опт-инный пилот — получает TG-нудж
    - Портной (DP.ROLE.027 / WP-149) — получает render-задачи через guide_render_queue
created: 2026-05-17
updated: 2026-05-17
wp: WP-326
---

# IWE Stage Controller — DP.ROLE.046

> # see DP.SC.139, DP.ROLE.046
>
> **Kind:** Background Controller Role — фоновый детектор + диспетчер. Без интерактивного диалога с пилотом; читает состояние, принимает решение по детерминированной таблице, инициирует действие (нудж или render).
> **Owner Role:** IWE Platform — исполнитель: tsekh-1 (NixOS), скрипт `iwe-stage-controller.py`, systemd timer.

---

## 1. Миссия

Удержать пилотов в движении по двумерной стадии мастерства IWE (cp.iwe × cp.cre), не давая «застрять» на одной точке без сигнала о следующем шаге.

Аналогия: тренер, который раз в день смотрит на каждого ученика и говорит «сегодня — вот этот один шаг». Не учит сам (это Портной), не оценивает уровень (это Диагност и Аттестатор) — только инициирует следующее действие.

## 2. Контекст

Имена стадий мастерства IWE (FORM.089 §4 cp.iwe и cp.cre) задают **рекомендательную** двумерную карту: ось управления (Пользователь 1-3) × ось создания (Разработчик 0-3). Маркеры `_IWE_MARKER_BY_STAGE` (FORM.089 §6.3) задают ожидаемый профиль на каждой ступени Ученика. Без активного актора, который ежедневно сравнивает фактический профиль с маркером и инициирует действие, эта карта остаётся декларацией — пилот её просто не замечает.

Контролёр закрывает этот разрыв.

## 3. Обязанности

| # | Обязанность | Артефакт-следствие |
|---|-------------|---------------------|
| 3.1 | Ежедневный обход всех опт-инных пилотов | Запуск systemd timer 06:00 МСК |
| 3.2 | Чтение текущей ступени Ученика (последняя `stage_transitions`) | в памяти процесса |
| 3.3 | Чтение текущего cp-профиля (последний валидный `cp_assessments`) | в памяти процесса |
| 3.4 | Применение детерминированной gap-логики по маркеру FORM.089 §6.3 | в памяти процесса |
| 3.5 | Проверка cool-down (3 дня по `content_hash` в `nudge_journal`) | Чтение `nudge_journal` |
| 3.6 | Решение канала: TG (простой шаг) vs render (сложный материал) | Бранч в коде по таблице DP.SC.139 |
| 3.7 | Отправка TG-нуджа через DP.ROLE.044 (Notification Dispatcher) | INSERT в `notification_log`, далее delivery via DP.SC.134 |
| 3.8 | Enqueue render-задачи в `learning.guide_render_queue` (для Портного) | INSERT в очередь |
| 3.9 | Запись решения в `learning.nudge_journal` (cool-down + аудит) | INSERT |
| 3.10 | Запись `iwe_stage_nudge_sent` / `iwe_stage_render_enqueued` в `domain_event` | INSERT в event log |
| 3.11 | Логирование метрик в `tsekh1-internal` (`iwe_stage_controller.runs_total`, etc.) | Prometheus push |

## 4. Полномочия

| Что может | Где |
|-----------|-----|
| Читать `learning.tracking_consent` (фильтр по opt_in = TRUE) | Neon learning |
| Читать `learning.stage_transitions` | Neon learning |
| Читать `learning.cp_assessments` | Neon learning |
| Читать `learning.nudge_journal` (cool-down) | Neon learning |
| Писать в `learning.nudge_journal` (новые записи) | Neon learning |
| Писать в `learning.domain_event` (event log) | Neon learning |
| Писать в `learning.guide_render_queue` (enqueue для Портного) | Neon learning |
| Вызывать `mcp__send_telegram_message` (через DP.ROLE.044) | Notification Dispatcher API |

## 5. Что роль НЕ делает

- **Не оценивает cp-профиль** — только читает результат Диагноста.
- **Не назначает ступень Ученика** — только читает результат Аттестатора.
- **Не интерпретирует свободно** — следует таблице маркеров (FORM.089 §6.3) и таблице «простой/сложный шаг» (DP.SC.139).
- **Не блокирует ступень** — cp.iwe и cp.cre informational.
- **Не пишет содержательный контент** — текст нуджа берётся из фиксированных шаблонов; сложный материал делегируется Портному через render-queue.
- **Не отправляет email/push** — только Telegram + guide_render.
- **Не работает с non-opt-in пилотами** — strict filter по `tracking_consent.opt_in`.

## 6. Single-runner

Контролёр обязан запускаться **в единственном экземпляре** — иначе риск дублирования нуджей (race на `nudge_journal`).

**Механизм:** `pg_try_advisory_lock(lock_key)` в `learning` БД при старте скрипта. `lock_key` — хэш строки `"iwe_stage_controller_v1"` (детерминированный int64). Если lock не получен — скрипт пишет в журнал «another instance running, skip» и завершается с exit 0 (это нормальная ситуация).

Lock освобождается автоматически при завершении сессии (PG advisory lock привязан к connection).

## 7. Связи с другими ролями

| Роль | Тип связи | Что передаётся |
|------|-----------|---------------|
| DP.ROLE.041 Аттестатор | upstream-data | `learning.stage_transitions` (ступень) |
| DP.ROLE.042 Диагност | upstream-data | `learning.cp_assessments` (cp-профиль, включая cp.iwe и cp.cre) |
| DP.ROLE.044 Notification Dispatcher | downstream-channel | TG-нуджи через DP.SC.134 |
| DP.ROLE.027 / WP-149 Портной | downstream-channel | render-задачи через `guide_render_queue` |
| DP.ROLE.022 / WP-203 Оркестратор | sibling (не конкурент) | Оркестратор реагирует на резкие отклонения bh-метрик (день-уровень); Контролёр работает на медленных оси cp (недели-месяцы) |

## 8. Тип ролевого ритма

Не реактивный (нет триггера-события), а **ежедневный обход** — pull-модель. Каждый день в фиксированное время сканирует всех opt-in пилотов и принимает решение по каждому независимо.

Это отличает контролёра от:
- **Аттестатора** (DP.ROLE.041): реагирует на новые события Activity Hub, обновляет stage_transitions, push-модель.
- **Диагноста** (DP.ROLE.042): запускается по запросу пилота (платформенный режим: chip/radio, ≤5 вопросов), не работает в фоне.
- **Оркестратора** (DP.ROLE.022): реагирует на резкие отклонения bh-метрик и calendar events — реактивный, не плановый.

## 9. Реализация

| Компонент | Где | Триггер |
|-----------|-----|---------|
| Скрипт `iwe-stage-controller.py` | tsekh-1 (NixOS), путь `~/iwe-server/scripts/` | manual + systemd timer |
| systemd timer `iwe-stage-controller.timer` | NixOS configuration.nix | `OnCalendar=*-*-* 06:00:00 Europe/Moscow` |
| systemd service `iwe-stage-controller.service` | NixOS configuration.nix | Запускается timer-ом |
| Таблица `learning.nudge_journal` | Neon `learning` DB | Создаётся миграцией WP-326 Ф3 |
| Очередь `learning.guide_render_queue` | Neon `learning` DB | Уже существует (WP-149) |

## 10. Метрики и наблюдаемость

См. DP.SC.139 §Метрики. Пуш через `write_internal_metric` (tsekh-1 internal pipeline).

## 11. История изменений

| Версия | Дата | Изменение | WP |
|--------|------|-----------|-----|
| 0.1 | 2026-05-17 | Первичная фиксация роли | WP-326 |
