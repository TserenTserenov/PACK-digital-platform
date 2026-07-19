---
id: DP.D.019
name: "DSL ≠ DSLM"
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

# DP.D.019: DSL ≠ DSLM

| DSL (Domain-Specific Language) | DSLM (Domain-Specific Language Model) |
|-------------------------------|---------------------------------------|
| Явный язык с синтаксисом | Implicit language в weights модели |
| Deterministic | Probabilistic |
| SQL, Terraform, GraphQL | Fine-tuned LLM на доменном корпусе |
| Стабильный, зрелый | Фронтир (2026) |

**Почему важно**: Принцип DSL ценен, инструментарий бифурцировал.

**Тест**: Можно ли записать правило как формальный синтаксис с парсером? Да → DSL. Нужна обученная модель? → DSLM.
