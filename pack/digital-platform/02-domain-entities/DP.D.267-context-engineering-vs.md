---
id: DP.D.267
name: "Context Engineering ≠ Prompt Engineering"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-05-08
created: 2026-07-19
renamed_from: DP.D.054
source: "миграция из 01B-distinctions.md, коллизия разрешена в пользу файла (РП170 Ф3, 2026-07-19)"
see_also: [DP.D.054]
schema_version: 1
---

# DP.D.267: Context Engineering ≠ Prompt Engineering

> **Перенумеровано при разрешении коллизии (2026-07-19, РП170 Ф3): DP.D.054 → DP.D.267.** Номер 054 оставлен за одноимённым файлом (решение по анализу контекстов ссылок — превосходство за файлом; реестр: `DS-my-strategy/inbox/bugs/drift-2026-07-18-dpd-numbering-collisions.md`). Ссылки на «DP.D.054» до 2026-07-19 могли означать эту сущность.

| Аспект | Prompt Engineering | Context Engineering |
|--------|-------------------|---------------------|
| **Суть** | Что **сказать** модели | Что **дать** модели |
| **Артефакты** | Instruction text, examples, templates | Tools, memory files, retrieval, constraints |
| **Цель** | Improve output quality | Enable task completion |
| **Пример** | «Think step-by-step» | MEMORY.md + Bash tool + PACK index + RAG |
| **Слой** | Микро (формулировка) | Макро (информационная среда) |

**Почему важно:** PE оптимизирует формулировку запроса; CE проектирует информационную среду вокруг модели. PE без CE = промпт без контекста (галлюцинации, узкое видение). CE без PE = богатая среда без точного запроса (агент тонет в шуме).

**Связь:** DP.SOTA.002 (Context Engineering как дисциплина) описывает *стратегии CE* (Write/Select/Compress/Isolate). DP.D.054 фиксирует *границу с PE*. AS.SOTA.006 Layer 2 описывает *эволюцию контекста* (ACE = self-evolving CE).

**Источник:** Context Engineering framework (Anthropic, 2026); WSCI Framework (Panat, 2026).
