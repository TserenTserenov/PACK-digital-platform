---
id: DP.D.255
name: "Один механизм в двух местах ≠ два слоя defense-in-depth (барьеры независимы только при семантической разнице)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform / security
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-28
created: 2026-07-18
source: "peer-session 2026-06-26-26 WP-436, turn 1; extraction-report 2026-06-28-inbox-check #3"
see_also: [DP.M.088, DP.FM.339]
schema_version: 1
---

# DP.D.255: Один механизм в двух местах ≠ два слоя defense-in-depth

Запуск одного и того же delta-aware grep в pre-commit **и** в CI — это не defense-in-depth, а **один слой, исполненный дважды**.

| Настоящий второй слой (defense-in-depth) | Один механизм в двух местах |
|-----------------------------------------|------------------------------|
| Семантически иная проверка (другой алгоритм, другой угол зрения) | Тот же алгоритм на тех же данных в другом окружении |
| Знающий правило не обходит оба одним приёмом | Один приём обходит оба места одновременно |
| Пример: delta-aware grep + граф вызовов / taint-analysis | Пример: pre-commit grep ≈ CI grep (разница — только где запускается) |

**Тест:** «Обходит ли знающий правило оба слоя одним приёмом?» Да → один слой дважды. Нет → настоящий defense-in-depth.

**Связано с:** [DP.M.088] (Pack-инварианты, defense-in-depth — источник: «CI + pre-commit = два барьера» верно только при семантической разнице между слоями). [DP.FM.339] (diff-aware guard слеп к indirect-обходу — следствие этой слабости).

**Источник:** peer-session 2026-06-26-26 WP-436, turn 1.
