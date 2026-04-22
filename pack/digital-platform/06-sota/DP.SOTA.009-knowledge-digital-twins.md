---
id: DP.SOTA.009
name: Knowledge-Based User Models (Persona / Memory / Projection)
type: sota
status: active
summary: "Персональные/enterprise knowledge graphs и user models как трёхслойная архитектура: декларативная Персона (user-owned), наблюдаемая Память (platform-owned), runtime Проекция (ephemeral). Эволюция термина 'digital twin' в LLM-эру."
created: 2026-02-13
updated: 2026-04-22
edition: "2026-04"
trust:
  F: 3
  G: external
  R: 0.55
related:
  integrates_with: [DP.SOTA.004, DP.SOTA.002]
  enables: [DP.CONCEPT.001, DP.EXOCORTEX.001, DP.CONCEPT.003]
tags: [persona, memory, projection, user-model, knowledge-graph, personal-ai, pkm]
---

# Knowledge-Based User Models (Persona / Memory / Projection)

## 1. Определение

**Knowledge-Based User Models** — модели, объединяющие доменное знание (ontology, knowledge graph) с данными реального пользователя (события, индикаторы, декларации) для создания «представителя», способного отвечать от лица владельца и принимать решения в его контексте. В индустрии ещё распространён термин **digital twin**, но по мере роста LLM-агентов монолит расслаивается на три сущности по критерию **writer + owner**:

| Слой | Writer | Owner | Аналог-SoTA |
|------|--------|-------|-------------|
| **Персона** | Пользователь | Git пользователя | Letta persona/human blocks, Mem0 user attributes |
| **Память** | Платформа runtime | Хранилище (Neon / Chroma) | Letta archival/recall, LangMem episodic, Mem0 raw memories |
| **Проекция** | Агент в runtime | Эфемерно | CQRS read-models, LLM Context Window, Open Learner Model |

## 2. Статус SOTA (апрель 2026)

**Emerging → consolidating.** Терминологическая смена: «digital twin» → «user model» / «agent memory architecture» / «persona + memory».

### 2.1 Прецеденты расщепления writer/owner

- **Letta (Berkeley, MemGPT 2023+):** явное разделение `persona` (декларация агента) / `human` (декларация пользователя) / `archival` (long-term memory) / `recall` (recent events) — прямой прецедент Персона+Память.
- **Mem0 (2024-2025):** отдельный путь для **structured user attributes** (≈ Персона) и **raw memories** (≈ Память.Observed). API отражает разницу источника.
- **LangMem (LangChain, 2024+):** три типа памяти — *semantic* (факты о пользователе, ≈ Персона), *episodic* (события, ≈ Память.Observed), *procedural* (как пользователь работает, ≈ Память.Derived).
- **Anthropic Memory tool (Claude Files, 2026):** user-editable memory blocks vs agent-observed — прямая поддержка Персоны как user-writable слоя.

### 2.2 SoTA по слоям

**Персона (user-declared):**
- Personal.ai (2025-2026): AI digital twins для PKM — реплицируют индивидуальную перспективу владельца.
- Lenovo Qira (CES 2026): «Fused Knowledge Base» — cross-device декларация контекста пользователя.

**Память (platform-observed):**
- Event Sourcing (Fowler 2005, Young 2010): append-only log + projection — архитектурная база Память.Observed + Derived.
- BKT / HLR как индустриальный стандарт для Derived-проекций обучения.

**Проекция (runtime):**
- CQRS read-models (Young 2010, Vernon 2016): отдельные read-модели под потребителя.
- Open Learner Model (Bull & Kay, 2007+): проекция под пользователя как учебный инструмент.
- Context Engineering (DP.SOTA.002): runtime-сборка LLM-контекста — прямой предок LLM Context Projection.

## 3. SPF-интеграция

Экзокортекс IWE — ранняя реализация этого тренда с явным writer+owner расщеплением:

| Слой | Writer | Owner | Экзокортекс-реализация |
|------|--------|-------|------------------------|
| **Персона** | Пользователь | Git пользователя | PACK-personal + DS-my-strategy + CLAUDE.md + extensions |
| **Память.Observed** | Платформа | Neon (#3 activity-hub, #4 payments) | events, sessions, payments, ai_chat |
| **Память.Derived** | Платформа | Neon (#5 digital-twin) | BKT, HLR, engagement, misconceptions, qualifications, baseline/potential |
| **Проекция LLM Context** | Агент runtime | Эфемерно | Портной, Стратег, Консультант, Диагност — собирают промпт |
| **Проекция User View** | Бот/web | Эфемерно | `/mydata` Open Learner Model |
| **Проекция Nudge** | Nudge Engine | Эфемерно | А/О/В/П/Вз/Тр/С сообщения через бот, email, web |

## 4. Правила применения

1. **Не монолит, а три сущности:** не складывать user-declared и platform-observed в одну таблицу/сущность. Критерий — writer + owner (DP.D.052).
2. **Персона ≠ Память ≠ Проекция** (HD #27): Git-пользователя vs Neon vs runtime.
3. **Платформа не пишет в Персону:** только пользователь (или агент по его поручению через acceptance flow).
4. **Проекция эфемерна:** не хранится; хранится только `*_log` событие о том, что Проекция была сделана.
5. **Проекция, не копирование** (из старого §4): downstream показывает view модели, не дублирует Pack.
6. **При проектировании user-data** — определить: что в platform-space (общее для всех), что в user-space (персональное), что в runtime (ephemeral)?

## 5. 4-Stage AI Framework для User Models (Scout 12 апр 2026)

> Источник: arxiv.org/abs/2601.01321 — «Digital Twin AI: Opportunities and Challenges from LLMs to World Models»

### Lifecycle фреймворк интеграции AI в user models

| Стадия | Описание | AI-роль | Маппинг на слои |
|--------|----------|---------|-----------------|
| **Modeling** | Создание модели системы | Классические методы | Онтология (Pack, ZP, FPF) |
| **Mirroring** | Синхронизация состояния | ML, sensor fusion | Память.Observed + Derived |
| **Intervening** | Рекомендации, оптимизация | LLM as advisor | Проекция (LLM Context + Nudge) |
| **Autonomous Management** | Саморегулирующийся user-model | Agents + foundation models | Агенты-писатели всех трёх слоёв |

Последняя стадия: user-model становится «proactive and self-improving cognitive system».

**Три критических challenge для Autonomous Management:**
1. **Scalability** — управление сложностью при росте числа пользователей и событий
2. **Explainability** — интерпретируемость решений агентов (особенно для Memory.Derived и Projection)
3. **Trustworthiness** — верификация автономных действий (особенно Agent-writes в Persona)

11 прикладных доменов (от промышленности до персональных систем).

**Импликация для IWE:**
IWE-платформа находится на стадии **Mirroring → Intervening** (Память.Derived через `dt_calc.py`, Проекция через Портной/Диагност). Autonomous Management = горизонт R6/R10. Три challenges соответствуют WP-212 (trustworthiness через security), WP-217 (explainability через capture flow), WP-218 (scalability через projection profiler).

## 6. Источники

- Letta (ex-MemGPT, Berkeley) — persona/human/archival/recall blocks, 2023+
- Mem0 — structured attributes vs raw memories, 2024-2025
- LangMem (LangChain) — semantic/episodic/procedural, 2024+
- Anthropic Memory tool (Claude Files) — user-editable blocks, 2026
- Fowler M. «Event Sourcing» — 2005
- Young G. «CQRS and Event Sourcing» — 2010
- Bull S., Kay J. «Open Learner Models» — 2007+
- Personal.ai. «AI Digital Twins: Future of PKM» (2025)
- Lenovo Qira. «Fused Knowledge Base» (CES 2026)
- arxiv.org/abs/2601.01321 — «Digital Twin AI: Opportunities and Challenges from LLMs to World Models» (2026) — §5

---

*Пересмотрено в WP-257 Ф5 (расщепление ЦД на Персона/Память/Проекция, 2026-04-22).*
