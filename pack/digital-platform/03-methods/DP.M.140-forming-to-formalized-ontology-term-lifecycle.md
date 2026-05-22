---
id: DP.M.140
type: method
name: "Двухфазный жизненный цикл онтологических терминов: forming → formalized"
domain: digital-platform
status: draft
trust: medium
epistemic_stage: instance
source: "PACK-personal commit c72ee57, feat ontology: добавлены 15 терминов для Guide 2 S7–S10"
valid_from: 2026-05-21
related:
  complements: [DP.M.139, DP.M.103]
---

# Двухфазный жизненный цикл онтологических терминов: forming → formalized

## Суть

Термины входят в онтологию Pack в два этапа:
1. **forming** — термин нужен для guide/документа, определение черновое, Pack-сущность ещё не создана
2. **formalized** — термин верифицирован, Pack-сущность создана, source = ID Pack-файла

## Frontmatter для forming-термина

```yaml
- id: ontology_term
  status: forming
  source: "docs 1-2 §7"   # ссылка на источник черновика
  # нет Pack ID
```

## Frontmatter для formalized-термина

```yaml
- id: ontology_term
  status: formalized
  source: "PD.FORM.089 §4"  # ссылка на Pack-файл
  pack_id: PD.FORM.089
```

## Инвариант

`status: forming` разрешает временную неполноту без нарушения OwnerIntegrity: guide-разработка идёт впереди Pack-формализации. Ни один forming-термин не должен оставаться в этом статусе дольше одной итерации формализации.

## Детектор долгов

`grep "forming" ontology.md` → список терминов, ожидающих Pack-формализации.

## Антипаттерн

Добавить термин в ontology.md без поля `status` → невозможно отследить, что нужна формализация; forming-долги становятся невидимыми.
