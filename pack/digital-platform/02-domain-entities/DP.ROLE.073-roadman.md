---
id: DP.ROLE.073
name: Дорожник
type: agential
kind: "Мониторинговая роль (отслеживание польз)"
owner_role: "R22 Оркестратор"
suprasystem: "Система персонального развития"
context: "Отслеживание P1–P9 (промежуточных польз) после завершения онбординга (onboarding_complete = true). Дорожник предлагает следующую доступную пользу и эскалирует к Навигатору при повторных отказах."
source_sc: "DP.SC.173"
status: draft
source_wp: "WP-406 Ф6"
updated: 2026-06-10
source_sessions:
  - "2026-06-10-07-wp406-value-milestones-design"
  - "2026-06-10-11-wp406-f6-artifacts"
  - "2026-06-10-16-wp406-f5-f6-onboarder"
---

# DP.ROLE.073: Дорожник

## 1. Определение

**Дорожник** — роль мониторинга промежуточных польз (P1–P9) у пользователей, прошедших онбординг.

Дорожник не ведёт программу развития (это Навигатор) и не вводит в сообщество (это Онбордер). Дорожник отслеживает: что человек реально получил от платформы, и предлагает конкретный следующий шаг.

**Ключевое ограничение:** Дорожник работает ТОЛЬКО при `onboarding_complete = true` в поле `development.user_state.onboarding_context`. До завершения X2/X3 за P-прогрессию отвечает Онбордер (DP.ROLE.067).

---

## 2. Входы

| Вход | Источник |
|------|----------|
| `user_id` | `users.telegram_id` |
| `user_milestones` | `development.user_milestones` (has_p1..has_p9) |
| `onboarding_complete` | `development.user_state.onboarding_context->>'onboarding_complete'` |
| `active_offers` | `user_milestone_offers WHERE response IS NULL OR response = 'ignored'` |
| `decline_history` | `user_milestone_offers WHERE response = 'declined'` |
| `spot_check_signal` | `development.user_state.onboarding_spot_check_due < NOW()` (от R46) |

---

## 3. Обязанности

- **Определять следующую пользу.** Перебрать P1–P9 по матрице DP.VM.001; выбрать первую незакрытую при соблюдении инвариантов DP.SC.173 §2. Пропускать P4 и P6 всегда.
- **Проверять onboarding_complete перед P7–P9.** Если `onboarding_complete != true` — не создавать оффер для P7, P8, P9 ни при каких условиях.
- **Создавать оффер.** Вставка в `user_milestone_offers` (тип пользы, канал, текст). Partial unique index в БД гарантирует один активный оффер на (user_id, milestone_p).
- **Фиксировать закрытую пользу.** При сигнале «принял» или срабатывании прокси-события — обновлять `user_milestones.has_p{n} = true`, `updated_at`.
- **Считать последовательные отказы.** Вести счёт `declined` из `decline_history` для каждой пользы по (user_id, milestone_p).
- **Эскалировать к Навигатору.** При `count(declined) >= escalate_after_declines` — отправить domain event к R27. Если R27 недоступен — поставить в очередь, повторить через 1 час.

---

## 4. Ожидания (expectations)

| От кого | Что ожидают |
|---------|-------------|
| **R22 Оркестратор** | Дорожник сигнализирует о P9 (финальная польза) — человек готов к следующему уровню |
| **R27 Навигатор** | Получает эскалацию с историей отказов (какая польза, сколько раз) и профилем пользователя |
| **DP.ROLE.067 Онбордер** | Фиксирует `has_p4 = true` и `has_p6 = true` при закрытии X3; записывает `onboarding_complete: true` в `development.user_state.onboarding_context` |
| **R46 Контролёр развития** | Читает `onboarding_spot_check_due` для spot-check запросов; не пишет в `onboarding_context` сам |

---

## 5. Методы

| Метод | Описание |
|-------|----------|
| **Next Milestone Selection** | Перебор P1–P9 по DP.VM.001; первая пропущенная при соблюдении инвариантов DP.SC.173 §2 |
| **Proxy Detection (P1)** | `message_count >= 3 AND session_active_sec > 90`; A/B-валидация retention-7d на первых 50 пользователях |
| **Escalation Protocol** | Подсчёт `declined` из `user_milestone_offers`; при `>= escalate_after_declines` → domain event к R27 |

---

## 6. Рабочие продукты

| Продукт | Хранилище |
|---------|-----------|
| Оффер пользы | `user_milestone_offers` (INSERT) |
| Закрытие пользы | `development.user_milestones.has_p{n} = true` (UPDATE) |
| Эскалация к Навигатору | domain event `MilestoneEscalated` |

---

## 7. Различение

> **Дорожник ≠ Онбордер (DP.ROLE.067).** Онбордер закрывает X2 и X3 (ориентация + траектория). Дорожник — механика P-прогрессии ПОСЛЕ этого. Граница: `onboarding_complete = true`. До этого момента P-прогрессия управляется Онбордером.
>
> **Дорожник ≠ Навигатор (R27).** Навигатор — мотивационная программа долгосрочного развития. Дорожник — точечный механизм: «следующий реальный шаг». Навигатор подключается только при повторных отказах.
>
> **Дорожник ≠ Контролёр развития (R46).** R46 сканирует разрывы и маркеры развития (pull-sensor). Дорожник управляет конкретными P-пользами: предлагает, фиксирует, эскалирует.

---

## 8. Текущие исполнители

| Исполнитель | Grade | Сценарии |
|-------------|-------|----------|
| **TBD** (WP-406 Ф5, реализация) | 2+ | Все сценарии DP.SC.173 |
