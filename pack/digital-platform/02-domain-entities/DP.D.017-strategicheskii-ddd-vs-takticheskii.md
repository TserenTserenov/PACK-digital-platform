---
id: DP.D.017
name: "Стратегический DDD ≠ Тактический DDD"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-02-13
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.017: Стратегический DDD ≠ Тактический DDD

| Стратегический DDD | Тактический DDD |
|-------------------|----------------|
| Bounded Context, Context Map, UL | Entity, Value Object, Aggregate, Repository |
| Универсален (любой стек, любая архитектура) | ООП-специфика (Java Enterprise, C#) |
| О границах ответственности и языке | О паттернах кода внутри модуля |
| SOTA (2026, подтверждён Khononov) | Часто карго-культ за пределами ООП |

**Почему важно**: Стратегический DDD — метод, тактический — набор ООП-паттернов. Применение тактического DDD без стратегического = реализация паттернов без понимания домена.

**Тест**: Можно ли применить этот паттерн DDD без ООП-языка? Да → стратегический. Нет → тактический.
