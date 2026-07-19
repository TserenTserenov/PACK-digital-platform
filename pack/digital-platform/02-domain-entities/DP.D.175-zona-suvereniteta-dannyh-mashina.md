---
id: DP.D.175
name: "Зона суверенитета данных = машина владельца, не managed cloud"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-07-01
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.175: Зона суверенитета данных = машина владельца, не managed cloud

**Описание:**
В суверенном гибриде (sovereign hybrid): алгоритм/метод обработки единый (централизован), исполнение с данными происходит на машине владельца, а не в managed cloud.

- **Managed cloud**: данные уходят в сторонний сервис для обработки → потеря суверенитета
- **Суверенный гибрид**: метод единый, данные остаются в своей зоне (tsekh-1 = личная машина пилота, не shared cloud server)

**Следствия для проектирования:**
1. **Downtime model**: машина может засыпать / уйти офлайн → delivery должна учитывать (snapshots, fallback, async)
2. **Data residency**: личные данные не уходят на сторонний управляемый сервис
3. **Deploy model**: шаблон разворачивается у каждого пользователя локально, нет единого shared processing cloud

**Тест:** «Где физически обрабатываются данные пользователя?» Если ответ — внешний управляемый сервис, это потеря суверенитета данных.

**Источник:** ADR-2026-06-30 (топология персональных руководств, ArchGate PASS).
**Смежно:** DP.D.172 (метод сборки ≠ место исполнения ≠ поверхность — ожидается pending), DP.D.030 (deployment topology), DP.D.059 (хранение credentials).
