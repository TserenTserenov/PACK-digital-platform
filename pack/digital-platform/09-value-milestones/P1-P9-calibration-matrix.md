---
id: DP.VM.001
title: "Calibration Matrix — P1-P9 Value Milestones"
valid_from: 2026-06-10
owner: platform
schema_version: 1
source: peer-session 2026-06-10-11-wp406-f6-artifacts (WP-406 Ф6)
related: [DP.SC.173, DP.ROLE.073, DP.ROLE.067, DP.SC.170]
---

# Calibration Matrix — P1-P9 Value Milestones

Матрица задаёт прокси-метрики для фиксации каждой «пользы» (has_P{n} = true)
в таблице `user_milestones`. Proxy confidence отражает надёжность прокси
на момент MVP; план валидации — как проверить что прокси работает.

> **Архитектурное правило:** `user_milestones` хранит факты (кто что пережил).
> Логика «когда предлагать следующую пользу» — в Дорожнике (DP.SC.173).
> Колонка `milestone_p_class` (pre/post onboarding) — не хранится в схеме;
> Дорожник проверяет `onboarding_complete` + `user_milestones` напрямую.

## Таблица

| P | Польза | Прокси для has_P{n} = true | Proxy confidence | Условие в БД | План валидации |
|---|--------|---------------------------|-----------------|--------------|----------------|
| P1 | Первый полезный ответ от ИИ | `ai_response_count >= 1 AND user_message_count >= 3 AND session_active_sec > 90` | medium | INSERT в user_milestones при первом достижении условия в event log | Корреляция `has_p1` с `retention_7d` на первых 50 пользователях. Если корреляция < 0.2 — прокси слабый, пересмотр. |
| P2 | Пробная тема | Открыт хотя бы один урок бесплатного курса (`lesson_events.type = 'free_preview'`) | high | INSERT при первом событии типа `free_preview` | Воронка: P2 → P3. Если P2 не ведёт к P3 в 30% случаев — контент пробника слабый, не прокси. |
| P3 | Марафон на регулярной основе | Пользователь подписан на марафон И получил хотя бы 3 урока (`marathon_checkins >= 3`) | high | INSERT после 3-го зафиксированного чек-ина в `marathon_state` | Retention 14d у когорты has_p3=true vs false. Ожидаемое: +15%. |
| P4 | Профиль в Aisystant | `user_profile.completed_at IS NOT NULL` (имя + цель + выбранная ступень) | high | INSERT при первом `user_profile.completed_at` | Операционная; профиль либо заполнен, либо нет. Ложные срабатывания маловероятны. |
| P5 | Первая маленькая победа | Ответил на вопрос урока ИЛИ записал мысль (`quiz_attempts >= 1 OR note_events >= 1`) | medium | INSERT при первом quiz_attempt или note_event | Распределение по типу события (quiz vs note). Если >90% — note без quiz → победа формальная, не содержательная. |
| P6 | Персональная траектория | `user_profile.bottleneck IS NOT NULL AND user_profile.recommended_stream IS NOT NULL` | high | INSERT после заполнения bottleneck + recommended_stream (часть Х3 Онбордера) | Онбордер закрывает P6 как часть Х3 — двойного трекинга нет, Дорожник просто видит has_p6=true. |
| P7 | Доступ к сообществу (клуб) | `club_memberships.active = true` | high | INSERT при первой активной записи в club_memberships | Операционная. Ложные срабатывания исключены FK-ограничением. |
| P8 | Цифровой двойник | `digital_twin.initialized_at IS NOT NULL` (хотя бы один cp-слот заполнен) | medium | INSERT при первом `digital_twin.initialized_at` | Качество цифрового двойника (cp-профиль vs эталон Диагноста) — отдельная метрика вне Дорожника. |
| P9 | Своя среда развития | Репозиторий DS-strategy создан И есть хотя бы 1 коммит (`github_repos.has_ds_strategy = true AND commit_count >= 1`) | medium | INSERT при первом коммите в DS-strategy репозиторий пользователя | Retention 30d у когорты has_p9=true. Ожидаемое: наивысший retention среди всех P. |

## Примечания

- **P1 — medium confidence:** три сообщения за 90 секунд не гарантируют «полезный» ответ.
  Валидация по корреляции с retention — первый реальный сигнал. До накопления 50 пользователей
  с `has_p1=true` — прокси работает «на доверии».

- **P4 и P6 — закрываются Онбордером:** в процессе прохождения Х3 (DP.SC.170) Онбордер сам
  обновляет `user_profile`. Дорожник (DP.SC.173) видит уже заполненные `has_p4=true` / `has_p6=true`
  и не предлагает их повторно.

- **Escalate after declines:** параметр Дорожника `escalate_after_declines` (default: 2).
  Не хардкод в этой матрице.
