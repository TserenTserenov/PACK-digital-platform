---
id: DP.D.105
name: "Pack-internal frontmatter check ≠ DS-level prose check (scope линтера)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-01
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.105: Pack-internal frontmatter check ≠ DS-level prose check (scope линтера)

| Аспект | Pack-internal frontmatter check | DS-level prose check |
|--------|--------------------------------|----------------------|
| **Источник нарушения** | Frontmatter поля внутри Pack-файла (id, type, disjoint-теги, type-mapping) | Misuse Pack-понятий в downstream-документах (prose, конкретные кейсы) |
| **Парсер** | Regex / YAML — детерминированный | Semantic parsing — требует контекста |
| **False-positive rate** | Низкий | Высокий |
| **Где живёт** | Линтер / CI-gate | Reviewer-checklist / Competency Questions |
| **Стоимость поддержки** | Линейная (новое правило → новый regex) | Растёт — требует обновлений промптов / семантики |

**Тест выбора:** «Парсится ли источник нарушения детерминированно?»
- Да → правило линтера, Pack-internal scope
- Нет → правило не линтерское — переносить в reviewer-checklist или CQ (см. [MIM.M.032](../../../PACK-MIM/pack/MIM/03-methods/MIM.M.032-competency-questions-as-pack-dod.md))

**Failure mode при смешении:** Раздутые примеры и over-engineered правила (наблюдался кейс с 80 D.* вместо 15 — линтер ловил DS-level misuse, не свою зону ответственности).

**Контекст:** Выявлено в peer-сессии 19 (use-ontology-engineering-in-packs, Тема 2, ход 4, 2026-05-27). Применимо к любым linter/validator системам, schema-validation, doc-style-checkers.
