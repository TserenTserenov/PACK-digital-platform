---
id: DP.D.018
name: "Model ≠ View (3 паттерна)"
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

# DP.D.018: Model ≠ View (3 паттерна)

| Model (модель) | View (представление) |
|----------------|---------------------|
| Изменение семантики, утверждений, границ | Изменение формы, подачи, потребителя |
| Source-of-truth (Pack) | Проекция (Downstream) |
| Одна | Много |

**3 паттерна:** viewOf(model), compositionViewOf(models[]), projectionView(model, concern).

**Почему важно**: Дублирование знаний = рассинхронизация. View проецирует, не копирует.

**Тест**: Требует ли изменение обновления Pack? Да → модель. Достаточно обновить downstream → view.
