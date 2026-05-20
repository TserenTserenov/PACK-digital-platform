---
id: DP.M.099
name: "Illustration as First-Class Pack Object"
type: method
description: "Хранить переиспользуемые иллюстрации/аналогии как Pack-объекты с explicit structural_relation, не встроенным текстом"
trust: confirmed
epistemic_stage: demonstrated
valid_from: 2026-05-19
---

# DP.M.099 Иллюстрации как первоклассные Pack-объекты

## Суть метода

Когда в Pack используются примеры, аналогии или иллюстрации для объяснения концептов — хранить их как формализованные Pack-объекты с frontmatter (audience_level, effect, concept, structural_relation), а не встроенным текстом.

## Инвариант

Возвращаемый объект-иллюстрация **сохраняет структурные отношения** концепта (не только поверхностное сходство). Тест: «можно ли объяснить через эту иллюстрацию любое следствие концепта?» Да → структурное, Нет → поверхностное.

## Шаги

1. Определить концепт, для которого нужна иллюстрация
2. Создать файл `{PREFIX}.ILL.{NNN}-{slug}.md` в директории Pack'а
3. Frontmatter: `concept`, `structural_relation` (что именно сохраняется), `audience_level`, `effect`
4. Хранить в Pack, ссылаться из контента по ID

## Тест применимости

«Есть ли примеры/аналогии, которые используются повторно в разных разделах?» Да → Pack-объект, не inline.

## Прецедент

PACK-rhetoric v0.1.0 (WP-340, 2026-05-19): RHE.ILL.001 (Болид), RHE.ILL.002 (Асептика), RHE.ILL.003 (Земмельвейс) как seed illustrations с RHE.SC.001-инвариантом.

## Связи

- Pack: RHE.D.001, RHE.D.002 — типология иллюстраций в PACK-rhetoric
- Service Clause: RHE.SC.001 — инвариант structural_relation
