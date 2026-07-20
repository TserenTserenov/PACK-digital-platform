---
id: DP.D.271
name: "pool.acquire(timeout) ≠ command_timeout в asyncpg"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-05-31
created: 2026-07-19
renamed_from: DP.D.113
source: "миграция из 01B-distinctions.md, коллизия разрешена в пользу файла (РП170 Ф3, 2026-07-19)"
see_also: [DP.D.113]
schema_version: 1
---

# DP.D.271: pool.acquire(timeout) ≠ command_timeout в asyncpg

> **Перенумеровано при разрешении коллизии (2026-07-19, РП170 Ф3): DP.D.113 → DP.D.271.** Номер 113 оставлен за одноимённым файлом (решение по анализу контекстов ссылок — превосходство за файлом; реестр: `DS-my-strategy/inbox/bugs/drift-2026-07-18-dpd-numbering-collisions.md`). Ссылки на «DP.D.113» до 2026-07-19 могли означать эту сущность.

- **pool.acquire(timeout) ≠ command_timeout в asyncpg** (DP.D.113). `command_timeout` защищает SQL-исполнение, НЕ ожидание в очереди соединений (`pool.acquire()`). При half-open TCP worker зависает на `acquire()`, а не на SQL → добавлять явный `pool.acquire(timeout=X)` во всех checkout-точках. Типичный gap: 4 места в 3 файлах (listener.py, db.py, matcher.py).

**Контекст:** Выявлено при диагностике event-loop stall в production asyncpg worker (2026-05-29, WP-358).
