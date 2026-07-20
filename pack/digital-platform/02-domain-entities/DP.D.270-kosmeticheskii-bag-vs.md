---
id: DP.D.270
name: "Косметический баг ≠ Operational alert"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-02
created: 2026-07-19
renamed_from: DP.D.107
source: "миграция из 01B-distinctions.md, коллизия разрешена в пользу файла (РП170 Ф3, 2026-07-19)"
see_also: [DP.D.107]
schema_version: 1
---

# DP.D.270: Косметический баг ≠ Operational alert

> **Перенумеровано при разрешении коллизии (2026-07-19, РП170 Ф3): DP.D.107 → DP.D.270.** Номер 107 оставлен за одноимённым файлом (решение по анализу контекстов ссылок — превосходство за файлом; реестр: `DS-my-strategy/inbox/bugs/drift-2026-07-18-dpd-numbering-collisions.md`). Ссылки на «DP.D.107» до 2026-07-19 могли означать эту сущность.

**Тест:** будит ли неверный output оператора?

| Косметический баг | Operational alert |
|------------------|-------------------|
| false-positive в read-only dashboard | false-positive в alert-source (пейджер, on-call) |
| оператор замечает «когда-нибудь» | оператор вызван в 09:00 субботы |
| backlog OK | править в той же сессии |
| severity = cosmetic | severity = operational |

**Почему важно:** распространённая ошибка triage — «неверная цифра / неточный SQL / wrong window» → косметика → backlog. Тест жёстче: если неверный output **порождает action** у оператора (просыпание, проверка, эскалация) — это operational, даже когда code-fix = одна строка.

**Антипаттерн:** alarm fatigue от cosmetic-fixed-as-cosmetic alerts накапливает desensitization к реальным incidents.

**Тест границы:** «если игнорировать этот баг 30 дней — кто-то будет разбужен / отвлечён зря?» Да → operational; нет → cosmetic backlog OK.

**Прецедент:** WP-7 pulse-daily-report (2026-05-30, peer-session 2026-05-30-17): UX Alert SQL-фильтр `activity_date = CURRENT_DATE` вместо 24h окна → false-positive «бот умер 0 DAU» в 09:00 субботы. Изначально классифицирован как «cosmetic», переклассифицирован в operational.
