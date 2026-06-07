---
id: DP.FM.138
type: failure-mode
title: Shared БД для staging и production без env-discriminator поля — кросс-окружение contamination
trust: observed
epistemic_stage: confirmed
domains: [staging-isolation, database, multi-tenant, ops]
source_session: 2026-06-06 session-close (WP-330 + WP-7 Ф-Pilot-LearningDB-Isolation)
source_commit: ac5d97d (inbox/WP-7-platform-tech-debt.md)
valid_from: 2026-06-06
schema_version: 1
---

# DP.FM.138 — Shared БД без env-discriminator → кросс-окружение contamination

## Симптом

Staging-окружение (pilot, dev, canary) использует ту же БД, что и production. Таблицы не имеют поля `env_id` / `bot_id` / `tenant_id`. Действия в staging записываются в те же строки, что production.

Типичный кейс: пользователь проходит урок на pilot-боте → запись в dedup-таблицу → на проде `/learn` возвращает «Урок уже отправлен», хотя продовый урок не отправлялся.

Причина диагноза затруднена: оба окружения «работают», данные технически консистентны — просто пересекаются.

## Корень

Отсутствие discriminator field во всех shared таблицах. При добавлении нового staging-окружения не проведён audit «какие таблицы используют оба окружения».

## Профилактика

**Правило:** staging и production могут делить БД только при наличии explicit discriminator field (`bot_id`, `env`, `tenant_id`) во **всех** shared таблицах.

Варианты:
1. **Отдельная БД для staging** — самый надёжный путь (нет риска contamination).
2. **Discriminator field** — добавить `env_id` / `bot_id` во все shared таблицы + index. Обязателен во всех dedup, session, progress таблицах.
3. **Промежуточный фикс** — сократить dedup-окно (720 → 60 мин) до полного разделения.

Тест: «Могут ли записи staging и production быть неотличимы по содержимому строки (без JOIN на meta-таблицу)?» Да → discriminator field обязателен ИЛИ отдельная БД.

## Применимо к

- Telegram-боты с pilot и production инстансами
- Staging/preview environments на shared Neon/Postgres
- Любые multi-tenant сценарии с shared деплоем

## Связано

- WP-330 + WP-7 Ф-Pilot-LearningDB-Isolation — источник
