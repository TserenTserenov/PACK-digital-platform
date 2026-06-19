---
id: DP.D.155
title: "Активный день — определение"
status: active
valid_from: 2026-06-19
owner: pilot
source: "peer-session 2026-06-19-01, пилот Цэрэн"
see_also: [DP.ECON.001, DP.SC.133, DP.SC.136]
---

# DP.D.155 — Активный день: определение

## Принцип

**Любое действие пользователя в любом интерфейсе = активный день.**

Интерфейсы, которые засчитываются:
- IWE (VS Code, claude.ai, CLI)
- Клуб (systemsworld.club): пост, тема, лайк, комментарий
- LMS: урок, задание, тест, текст, таблица
- Бот (Telegram)
- Git-активность (коммит, контент)
- Помодоро, слоты, KE/Pack-работа

## Правило

Все действия из таблицы ниже засчитываются как «активный день» И начисляют баллы (см. BASE_AMOUNTS в reward_config.py).

| Группа | Event types |
|--------|------------|
| IWE | `iwe_session`, `day_close`, `work_session`, `session_complete` |
| LMS | `lesson_completed`, `task_submitted`, `text_submitted`, `table_submitted`, `test_passed`, `comment_created`, `topic_created`, `feed_completed`, `marathon_step`, `marathon_task` |
| Клуб | `club_post_created`, `club_topic_created`, `club_like_created`, `club_comment_received` |
| Git | `commit_created`, `git_commit`, `coding_time`, `content_published`, `fmt_commit_merged` |
| KE / Pack | `knowledge_extracted`, `note_to_capture`, `pack_updated`, `distinction_added`, `method_described` |
| ОРЗ / РП | `strategy_session_completed`, `wp_completed`, `slot_logged`, `pomodoro_completed` |

## Где применяется

- **orchestrator.py `_SESSION_EVENT_TYPES`** — slot_miss детектор: «сколько дней без активности»
- **reward_config.py `STREAK_ELIGIBLE`** — streak: дни подряд с активностью
- **render-pilot-guides.py** — метрика M1 (active_days_30d)

## Исключения (НЕ считаются активным действием)

Системные события без инициативы пользователя: `tier_changed`, `notification_sent`, `dt_recalc`, `request_traced`, `personal_guide_updated`, `nudge_sent`.

## Обоснование

Принцип OwnerIntegrity: «один факт — одно место» — определение активного дня должно быть единым для всех компонентов системы. Дублирование (когда оркестратор считает активными одни события, а стрик-трекер — другие) порождает расхождение между «ты не занимался 30 дней» и реальностью.
