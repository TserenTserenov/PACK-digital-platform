---
id: DP.D.152
name: "Corpus-split строчный тег (decision-уровень) ≠ Сессионный тег (session-уровень)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-19
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.152: Corpus-split строчный тег (decision-уровень) ≠ Сессионный тег (session-уровень)

Строчный тег: два поля на каждую `decision`-запись agent_trace — `source` (origin: peer-session-import/session-close/hook) + `attributed_to` (autonomous/consensus/user-override). Сессионный тег: один тег на всю сессию. Паттерн-майнер R33 работает на уровне решений (decision-записей), не сессий → сессионный тег требует JOIN к сессии на каждой агрегации и не различает «агент автономно согласился» от «агент уступил под давлением напарника» в одной сессии.

**Тест:** «Нужно ли изолировать диалоговые уступки от автономных решений при паттерн-майнинге?» Да → строчный тег. DEFAULT-значения сохраняют обратную совместимость с existing hook. Источник: WP-295 Ф2 peer-session 2026-06-18, commit caeddb591, migration neon-migrations/mvp/266-wp295-f2-decision-source.sql.
