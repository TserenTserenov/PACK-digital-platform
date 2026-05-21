---
id: DP.M.116
name: Решение о распределении captures по Pack (Вариант B > Вариант A)
name_ru: Pack Source Distribution Decision
name_en: Pack Source Distribution Decision (Variant B vs New Pack)
type: method
status: active
summary: "При KE из нового источника: предпочтительно распределить по существующим Pack (Вариант B), а не создавать новый Pack (Вариант A). Вариант A оправдан только при: (1) принципиально новый домен, или (2) ≥30% сущностей не вписываются ни в один существующий Pack."
created: 2026-05-20
trust:
  F: 3
  G: domain
  R: 0.80
epistemic_stage: validated
related:
  uses: [DP.M.103]
tags: [pack, knowledge-management, extraction, decision-rule, ke-process]
wp: WP-345
---

# Pack Source Distribution Decision (DP.M.116)

## 1. Контекст

При KE из нового источника (книга, статья, корпус) возникает вопрос: создать новый Pack или распределить знания по существующим?

## 2. Метод

**Вариант A (новый Pack):** создать PACK-{source-name}. Использовать когда:
- Источник покрывает принципиально новый домен без пересечений с существующими
- ≥30% сущностей не вписываются ни в один существующий Pack
- Источник будет единственным SoT для этого домена (активная разработка, CI нужен)

**Вариант B (распределить по существующим):** по предметной принадлежности. Использовать по умолчанию когда:
- Captures укладываются в существующие Pack-домены без orphan-сущностей
- Методы → PACK-MIM или PACK-digital-platform/03-methods/
- Паттерны платформы → PACK-digital-platform
- Различения → 01B-distinctions.md целевого Pack
- Принципы → PACK-agent-rules или FPF

## 3. Пример (WP-345 BORO)

22 кандидата из книги Chris Partridge «Business Object Reference Ontology»:
- Вариант A = PACK-boro (новый)
- Вариант B = 18 unique + 3 fold-ins: PACK-MIM (5M+1S+6D), PACK-DP (2 SOTA), PACK-AA, PACK-personal
- Выбран B: все captures укладываются в существующие домены, нет orphan-сущностей

## 4. Правило

Вариант B снижает фрагментацию Pack-экосистемы: N источников → M Pack (M << N).
Новый Pack = overhead (lifecycle, governance, CI), оправданный только при реальном новом домене.
