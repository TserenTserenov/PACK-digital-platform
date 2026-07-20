---
id: DP.D.269
name: "content_role ≠ process_position (двухосная модель роли в multi-agent peer-сессии)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-02
created: 2026-07-19
renamed_from: DP.D.104
source: "миграция из 01B-distinctions.md, коллизия разрешена в пользу файла (РП170 Ф3, 2026-07-19)"
see_also: [DP.D.104]
schema_version: 1
---

# DP.D.269: content_role ≠ process_position (двухосная модель роли в multi-agent peer-сессии)

> **Перенумеровано при разрешении коллизии (2026-07-19, РП170 Ф3): DP.D.104 → DP.D.269.** Номер 104 оставлен за одноимённым файлом (решение по анализу контекстов ссылок — превосходство за файлом; реестр: `DS-my-strategy/inbox/bugs/drift-2026-07-18-dpd-numbering-collisions.md`). Ссылки на «DP.D.104» до 2026-07-19 могли означать эту сущность.

**Источник:** WP-367 Ф5, peer-session 2026-05-30-06, DP.SC.154 v4 (commit a65df76).
**Standalone-определение:** см. [DP.SC.154 §3.1, §6.2, §13](../08-service-clauses/DP.SC.154-multi-agent-conversational-session.md).

| Ось | Что задаёт | Возможные значения | Источник |
|-----|-----------|--------------------|----------|
| **content_role** | Домен-контекст (4 аспекта: критерии метода, формат артефакта, критерии качества, связи с другими ролями) | `DP.ROLE.NNN` \| `MIM.R.NNN` \| `ad-hoc:<имя>` | Pack или `meta.yaml.ad_hoc_roles` |
| **process_position** | Координация хода (lock + escalation invariants) | `writer` \| `peer` | Default `initiator = writer`; перенос через `SWAP_WRITER` (ACK/NACK) |

**Default rule:** инициатор сессии = writer по умолчанию, без обсуждения в Opening. Opening обсуждается только для content_role.

**SWAP_WRITER** = explicit маркер передачи writer-позиции с ACK/NACK другой стороны и записью в `meta.yaml.swap_history`. Нужен потому что lock-инвариант требует ровно одного writer; «два writer одновременно» = collision (нелегитимная комбинация).

**ROLE_DRIFT** — смена content_role в середине сессии:
- **Incremental** (тот же домен/семейство) → маркер `ROLE_DRIFT_INCR` в реплике, сессия продолжается.
- **Fundamental** (смена домена) → закрываем сессию + открываем новую с правильным `content_role`.

**Почему не трёхосная модель** (content × process × initiative): initiative — session-invariant (фиксируется в Opening), не per-turn атрибут; дублируется на 10 реплик; 3! комбинаций не оправданы 2² базой.

**Тест границы:** «изменилось ли что — *что* делает участник или *как* координирует ход?» Что → content_role. Как → process_position.

**Применимость:** любой turn-based ролевой протокол (peer-programming, code review, brainstorming), где у участника есть и доменная экспертиза, и процессная функция на ходу.
