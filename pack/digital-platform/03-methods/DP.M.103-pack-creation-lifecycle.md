---
id: DP.M.103
name: Жизненный цикл создания доменного Pack (7 фаз)
name_ru: Pack Creation Lifecycle от SOTA до навигационной карты
name_en: Pack Creation Lifecycle (7-phase)
type: method
status: active
summary: "Полный lifecycle создания нового Pack: Ф1 (онтология + SOTA) → Ф2 (различения) → Ф3.5 (extraction из корпуса) → Ф4 (IntegrationGate) → Ф5 (batch mining) → Ф7 (MAP + CHANGELOG + README + SPF 09-11). IntegrationGate до extraction = правильный порядок. SPF 09-11 = обязательное завершение."
created: 2026-05-19
trust:
  F: 3
  G: domain
  R: 0.80
epistemic_stage: validated
related:
  uses: [DP.M.058]
  references: []
tags: [pack, knowledge-management, lifecycle, extraction, sota, map, spf]
wp: WP-340
---

# Pack Creation Lifecycle: 7 фаз (DP.M.103)

## 1. Контекст

Создание нового Pack без чёткого порядка фаз приводит к: extraction без онтологии (неструктурированные данные), IntegrationGate после реализации (слишком поздно), Pack без навигационной карты (непригоден для навигации).

## 2. Фазы

| Фаза | Содержание | Ключевой артефакт |
|------|-----------|-----------------|
| Ф1 | Онтология домена + SOTA-сбор | `00-pack-manifest.md`, `08-sota/` |
| Ф2 | Базовые различения | `01B-distinctions.md` (core) |
| Ф3.5 | Extraction из существующего корпуса | Batch extraction-report из исторических материалов |
| Ф4 | IntegrationGate | Service Clause (DP.SC) + Use cases + Role (DP.ROLE) |
| Ф5 | Batch mining (ночной планировщик) | Основной корпус Pack (ILL, FORM, METHOD...) |
| Ф6 | Review + refinement | Правки inconsistencies |
| Ф7 | MAP + CHANGELOG + README + SPF 09-11 | v1.0.0 release-ready |

## 3. Ключевые принципы

**IntegrationGate до extraction (Ф4 перед Ф5):** Обещание Pack должно быть определено (кто потребитель, что получает) до наполнения содержимым — иначе extraction без ориентира.

**Extraction из существующего > с нуля:** Ф3.5 использует исторический контент (курсы, ДЗ, переписки, руководства) — быстрее и богаче чем создание с нуля.

**SPF 09-11 завершает lifecycle:**
- SPF §09 — SOTA review cadence (когда обновлять)
- SPF §10 — MAP публикация (навигация по домену)
- SPF §11 — Community review cycle

Без карты (MAP) навигация по Pack невозможна даже при полном наполнении.

## 4. Связи

- Предшествующий: DP.M.058 (Domain-Pack Creation Gate) — решение о необходимости нового Pack
- Пример реализации: PACK-rhetoric v1.0.0 (WP-340, 19 мая, от Ф1 до Ф7 за одну сессию)
