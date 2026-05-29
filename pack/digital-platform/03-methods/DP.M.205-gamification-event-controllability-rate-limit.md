---
id: DP.M.205
name: Gamification Rate Limit by Event Controllability
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
valid_from: 2026-05-28
source: session-transcript 2026-05-28 (WP-327 club events G4) + commit 9e26f08b
---

# DP.M.205 — Gamification Rate Limit: Event Controllability Determines Limit Type

## Описание

Критерий лимита gamification-событий — степень управляемости события пользователем.

## Классификация событий

| Тип | Определение | Лимит |
|-----|------------|-------|
| **Активное** | Пользователь инициирует напрямую (`like_created`, `post_created`, `comment_created`) | Строгий дневной cap (например, 2/день) |
| **Пассивное** | Реакция системы или окружения (`like_received`, `badge_granted`, `trust_level_changed`) | Отдельный cap или без cap |

## Алгоритм

При добавлении нового события в gamification model — первый вопрос:

> «Пользователь инициирует это событие или система/другой пользователь?»

- **Пользователь → активное** → строгий cap (против накрутки)
- **Система/другой → пассивное** → независимый cap или отсутствие cap

## Анти-паттерн

Единый лимит 3/день на `post_created` и `like_received` одновременно. Второй лимит бессмысленен: пользователь не может создать 100 входящих лайков сам → cap не несёт смысла.

## Применимо

Любая gamification-система с point events: Discourse, Telegram-бот с очками, community platform, learning management system.

## Связанные методы

- DP.M.200 — Self-financing referral mechanism
- DP.M.166 — Referral credit not points
