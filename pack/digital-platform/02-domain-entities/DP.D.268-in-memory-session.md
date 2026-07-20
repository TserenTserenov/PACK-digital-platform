---
id: DP.D.268
name: "In-memory session state (auto-cleanup on redeploy) ≠ Persistent session state без TTL (zombie accumulation)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-05-28
created: 2026-07-19
renamed_from: DP.D.103
source: "миграция из 01B-distinctions.md, коллизия разрешена в пользу файла (РП170 Ф3, 2026-07-19)"
see_also: [DP.D.103]
schema_version: 1
---

# DP.D.268: In-memory session state (auto-cleanup on redeploy) ≠ Persistent session state без TTL (zombie accumulation)

> **Перенумеровано при разрешении коллизии (2026-07-19, РП170 Ф3): DP.D.103 → DP.D.268.** Номер 103 оставлен за одноимённым файлом (решение по анализу контекстов ссылок — превосходство за файлом; реестр: `DS-my-strategy/inbox/bugs/drift-2026-07-18-dpd-numbering-collisions.md`). Ссылки на «DP.D.103» до 2026-07-19 могли означать эту сущность.

| Аспект | In-memory state | Persistent без TTL |
|--------|-----------------|-------------------|
| **Поведение при redeploy** | Очищается автоматически | Остаётся навечно |
| **Zombie sessions** | Нет — redeploy = garbage collector | Накапливаются без TTL/cron |
| **Failure mode** | Потеря активных сессий при сбое | Orphan-записи растут бесконечно |
| **Применимость** | Краткоживущие сессии (диалоги, wizard) | Требующие сохранения после сбоя |

**Тест выбора:** «Нужно ли сохранять сессию после сбоя/рестарта сервера?»
- Нет → in-memory (cleanup при redeploy = feature)
- Да → persistent с явным TTL/cleanup job

**Почему важно:** «In-memory с потерей» интуитивно воспринимается как слабость. Но для краткоживущих диалоговых сессий потеря при redeploy = автоматический cleanup zombie-сессий. БД без TTL накапливает orphan-записи. Ключевой критерий — lifecycle объекта, не «надёжность» как абстрактное свойство.

**Контекст:** Выявлено при ArchGate WP-358 Ф10 (2026-05-28), проектирование session tracker в Telegram-боте.

→ см. `DP.D.103-in-memory-vs-persistent-session-state.md`
