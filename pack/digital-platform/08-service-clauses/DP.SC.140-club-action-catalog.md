---
id: DP.SC.140
title: Каталог действий клуба
type: service_clause
status: active
domain: digital-platform
owner: DP.ROLE.036
realizes:
  - DP.SC.128
related:
  - DP.SC.122
  - DP.ROLE.036
  - DP.ROLE.041
schema_version: 1
valid_from: 2026-05-17
---

# DP.SC.140 — Каталог действий клуба

## Обещание

Каждое содержательное действие участника `systemsworld.club` (Discourse) учитывается платформой:
- одним типом события в `learning.domain_event` с `source='club_discourse'`;
- начислением баллов через `multi-domain-projection-worker` (DP.SC.122) согласно `reference.reward_rules`;
- маркером BH-метрики для Аттестатора (DP.ROLE.041) через `reference.bh_dimension_map`.

Set действий и их числовое выражение — открытый каталог, конфигурируемый через таблицы Neon без изменения кода.

## Триггер

Discourse webhook → `POST /club/webhook` на event-gateway (DP.SC.020) с HMAC-signature → mapping Discourse event_type в один из club_* типов.

## Каталог действий (на 2026-05-17)

| Действие | event_type | Лимит | BH-маркер | База (баллы) |
|----------|-----------|-------|-----------|--------------|
| Новая тема | `club_topic_created` | 1/день | bh.inv (medium) | 12 |
| Комментарий | `club_post_created` | 3/день | bh.inv (weak) | 5 |
| Лайк | `club_like_created` | 3/день | — | 2 |
| Регистрация | `club_user_created` | 1/день | — | 5 |
| Подтверждение email | `club_email_confirmed` | 1/день | — | 2 |
| Trust 1→2 | `club_trust_promoted` (`to_level=2`) | по событию | bh.sys (strong) | 25 |
| Trust 2→3 | `club_trust_promoted` (`to_level=3`) | по событию | bh.inv + bh.awr (strong) | 70 |
| Trust 3→4 | `club_trust_promoted` (`to_level=4`) | по событию | — | 0 |
| Ответ принят как решение | `club_solution_accepted` | 5/день | bh.awr (strong) | 12 |
| Привёл нового по приглашению | `club_invite_accepted` | 5/день | — | 35 |
| Значок бронзовый | `club_badge_granted` (`badge_type=bronze`) | 3/день суммарно по значкам | если из «маркеров регулярности» — bh.sys, обучающие — bh.inv | 3 |
| Значок серебряный | `club_badge_granted` (`badge_type=silver`) | (в общем лимите) | то же | 15 |
| Значок золотой | `club_badge_granted` (`badge_type=gold`) | (в общем лимите) | то же | 46 |

**Множители поверх базы:**
- Активность: `learning ×3` (все club_* типы)
- Ступень Ученика: ст.1 ×1.0, ст.2 ×1.2, ст.3 ×1.5, ст.4 ×2.0, ст.5 ×2.5

**Дневной общий cap для learning:** 100 баллов на ст.3 (вторичная защита поверх индивидуальных лимитов).

## Значки клуба — маркеры BH

Из 51 активного значка Discourse, маркерами мастерства Аттестатора являются:

**bh.sys (системность):**
- «Энтузиаст» (10 дней подряд) — strong
- «Поклонник» (100 дней подряд) — very_strong
- «Преданность» (365 дней подряд) — very_strong
- «Годовщина» (год + минимум пост) — medium

**bh.inv (инвестирование):**
- «Квалифицированный участник» (основное обучение клуба) — strong
- «Лицензированный участник» (дополнительное обучение) — very_strong

Остальные значки начисляют только баллы, в маркеры мастерства не входят.

## Источники правды

- `reference.event_type_domain_map` — каждый event_type → activity_domain
- `reference.reward_rules` — числовые правила (`amount`, `match_condition` для дискриминации по `to_level` / `badge_type`)
- `learning.club_action_limits` — дневные лимиты (`daily_limit`, `limit_group` для общих лимитов значков)
- `reference.bh_dimension_map` — связки event_type → bh.sys / bh.inv / bh.awr
- `reference.activity_domain_multipliers`, `reference.student_stage_multipliers` — множители

## Режим отказа

- Webhook HMAC invalid → 401, событие не учитывается. Discourse retry'ит автоматически.
- Неизвестный event_type → 200 ack без записи (Discourse не retry'ит).
- БД недоступна → 503, Discourse retry'ит до 30 раз.
- Bridge не resolved (нет linked в `club.members`) → событие записывается с `account_id=NULL`, periodic reconciler заполнит постфактум.

## Инвариант

Один факт (action в клубе) → одна строка в `domain_event` (идемпотентность по `(source, external_id)`). Повторный webhook с тем же `external_id` → 200 idempotent, дубля не создаётся.

## Связанные обещания

- DP.SC.128 — Ingest активности клуба (parent)
- DP.SC.122 — Rewards Projection (downstream, начисляет баллы по правилам отсюда)
- DP.SC.123 — Platform Observability (потоковая видимость club_* событий)
- DP.SC.137 — Rewards Analytics (агрегация баллов клуба в дашбордах)
