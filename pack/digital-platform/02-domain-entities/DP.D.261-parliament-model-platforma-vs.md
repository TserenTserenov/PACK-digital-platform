---
id: DP.D.261
name: "Parliament Model (Платформа) ≠ Local Coordination Hub (IWE)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-02
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.261: Parliament Model (Платформа) ≠ Local Coordination Hub (IWE)

> Перенумеровано при миграции (2026-07-19, РП170 Ф2): DP.D.102 → DP.D.261. Номер 102 занят сущностью «Четыре канала событий» (секция и файл). Реестр: `DS-my-strategy/inbox/bugs/drift-2026-07-18-dpd-numbering-collisions.md`.

**Источник:** session-transcript 2026-05-26 + peer-сессия 2026-05-26-16-wp337-parliament-boundary (report.md, тема 3) + commit 3ffe572a (WP-337/И refactor).

**Различение.** Parliament Model — паттерн (как MVC), реализуемый на двух уровнях с разными инвариантами. **Parliament Model** = Platform-уровень (Координатор + доменные агенты + замок + верификатор в облаке Neon/GKE). **Local Coordination Hub** = IWE-уровень (Local Session Manager: peer-discovery + file-lock, offline-capable, single-node). **Platform Adapter** (DP.IWE.011) = мост между уровнями.

**Критерий разграничения:**
- offline-инвариант (IWE должен работать без сети) → Local Coordination Hub
- плоский режим (новый пилот без аккаунта) → Local Coordination Hub
- multi-tenant + аудит + RBAC → Parliament Model (Platform)

**Тест:** «требуется ли работа без сети для одного пользователя?» Да → Local Coordination Hub. Нет (multi-tenant, облачные сервисы, аудит) → Parliament Model.

**Антипаттерн:** объединять под одним именем «Parliament Model» обе реализации → архитектурный дрейф, путаница в обещаниях (инварианты разные).

**Применимо:** к любым distributed-паттернам, реализуемым на двух уровнях (single-node offline vs cloud multi-tenant) — не сливать терминологически, разнести имена.

**Связи:**
- DP.IWE.005 Local Gateway (IWE-уровень реализация)
- DP.IWE.003 Aisystant MCP / Cloud Gateway (Platform-уровень реализация)
- DP.IWE.011 Platform Adapter (мост)
