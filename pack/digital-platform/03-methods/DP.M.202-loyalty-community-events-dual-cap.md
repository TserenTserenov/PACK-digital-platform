---
id: DP.M.202
name: "Loyalty: отдельная группа community events с двумя независимыми лимитами"
type: method
domain: digital-platform
status: active
trust: confirmed
epistemic_stage: validated
valid_from: 2026-05-28
source: "WP-327 v4.4, session 2026-05-28"
schema_version: 1
---

# DP.M.202 — Loyalty: Community Events Dual-Cap

## Описание

Community engagement events (полученные реакции, созданный контент, рост траста) выделяются в отдельную группу с двумя независимыми ограничениями: per-action cap (антиспам) и daily total cap (суммарный предел за день).

## Механизм

```
Группа G4 (community events):
  Входящие:  like_received, comment_received, topic_created,
             trust_promoted, badge_granted
  Исходящие: like_created (action, а не reaction)

Per-action cap (антиспам):
  like_created: ≤ 2 раз/день считается (независимо от daily total)

Daily total cap:
  Суммарная сумма баллов из G4 ≤ daily_cap_community
```

## Ключевые свойства

- **Два ограничения ортогональны:** per-action cap по одному событию может быть исчерпан, другой — нет
- **Anti-spam через per-action:** исключает накрутку баллов массовыми likes
- **Daily total cap:** предотвращает монетизацию через спам-контент
- **Reaction events (полученные)** — пассивный сигнал (like_received), логика доверия сообщества; ограничены only daily total
- **Action events (исходящие)** — активный сигнал (like_created), дополнительный per-action cap

## Применимость

Любая платформа с community + gamification: форум, marketplace, learning community. Особенно важно при интеграции с внешними форумами (Discourse, Slack), где события генерируются вне контроля платформы.

## Антипаттерн

Единый cap на все события — не различает «получил реакцию» и «сделал действие». Накрутчик получает те же ограничения, что и активный участник сообщества.

## Связи

- DP.M.200 (Самофинансирующийся реферальный механизм) — смежный паттерн лояльности
- WP-327 (points model design, 2026-05-28)
