---
id: DP.SOTA.009
name: Knowledge-Based User Models (Persona / Memory / Projection)
type: sota
status: active
summary: "Персональные/enterprise knowledge graphs и user models как трёхслойная архитектура: декларативная Персона (user-owned), наблюдаемая Память (platform-owned), runtime Проекция (ephemeral). Эволюция термина 'digital twin' в LLM-эру."
created: 2026-02-13
updated: 2026-05-31
edition: "2026-05"
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
| **Персона** | Пользователь | Distributed: Ory anchor + Git declarations + Neon refs (см. DP.D.052 v2) | Letta **human block** (НЕ persona block — там self-описание агента); Mem0 structured user attributes; LangMem semantic; OLM Learner Profile (Bull & Kay 2007); Solid user pod (W3C); Honcho peer-representation (см. §2.3) |
| **Память** | Платформа runtime | Хранилище (Neon / Chroma) | Letta archival/recall; LangMem episodic + procedural; Mem0 raw memories; OLM Learner Model (computed); Honcho dream-time derived |
| **Проекция** | Агент в runtime | Эфемерно | CQRS read-models; LLM Context Window; Open Learner Model view (Bull & Kay); nudge-message |

## 2. Статус SOTA (апрель 2026)

**Emerging → consolidating.** Терминологическая смена: «digital twin» → «user model» / «agent memory architecture» / «persona + memory».

### 2.1 Прецеденты расщепления writer/owner

> **Уточнение терминологии Letta (важно, 2026-05-31).** Прежняя редакция этой секции утверждала «Letta persona/human blocks — прямой прецедент Персона+Память». Это **неточно**. У Letta:
> - `persona block` = self-описание **агента** (тон, поведение, личность ИИ-агента) — это **НЕ** модель пользователя
> - `human block` = что агент знает о **пользователе** (декларации, факты) — это **наш коррелят Персоны**
> - `archival` / `recall` = long-term/recent memory агента — наш коррелят Памяти.Observed/Derived
>
> При импорте Letta-терминологии в IWE: наш «Персона» = их `human block`, не `persona block`. Чтение Letta-документации с предположением что их `persona` = наша Персона ведёт к путанице (см. peer-сессия 2026-05-31-11).

- **Letta (Berkeley, MemGPT 2023+):** разделение `persona block` (тон агента) / `human block` (декларация пользователя — коррелят нашей Персоны) / `archival` (long-term memory) / `recall` (recent events) — прецедент по разделению user-declared vs platform-observed, но с другим именованием.
- **Mem0 (2024-2025):** отдельный путь для **structured user attributes** (≈ Персона) и **raw memories** (≈ Память.Observed). API отражает разницу источника.
- **LangMem (LangChain, 2024+):** три типа памяти — *semantic* (факты о пользователе, ≈ Персона), *episodic* (события, ≈ Память.Observed), *procedural* (как пользователь работает, ≈ Память.Derived).
- **Anthropic Memory tool (Claude Files, 2026):** user-editable memory blocks vs agent-observed — прямая поддержка Персоны как user-writable слоя.
- **Solid (Tim Berners-Lee, W3C, ongoing):** explicit разделение user-pod (декларативные данные пользователя под его контролем) vs application-state — это **writer+owner split на уровне веб-стандарта**. Сильнейший внешний якорь для нашего критерия. См. solidproject.org.
- **W3C Decentralized Identifiers (DID, 2022):** identity-anchor живёт независимо от application-state — прямое архитектурное обоснование distributed identity pattern для Персоны.
- **OAuth 2.0 / OIDC federated identity (RFC 6749, 7591):** identity provider vs application — паттерн на котором стоит наша связка Ory ↔ application state.

### 2.3 Альтернативный паттерн: unified peer (Honcho)

**Honcho (Plastic Labs, 2024-2026)** выбирает противоположный подход — **unified peer model** вместо writer+owner расщепления.

- Архитектура: `workspaces → peers → sessions → messages`. Humans и AI agents — first-class peers одного типа.
- Two-stage pipeline: *ingest-time* (small fine-tuned model извлекает preferences/claims/observations из каждого сообщения, обновляет peer representation сразу) + *dream-time* (background reasoning — derives latent patterns).
- Нет explicit writer+owner критерия: peer representation derives implicitly из переписки, не разделяет «декларация пользователя» vs «наблюдение платформы».

**Почему IWE отвергает unified peer pattern:**
1. **GDPR consent-flow:** declarative content (Персона) и observed events (Память) подпадают под разные согласия (Art. 6 GDPR). Implicit derivation смешивает их → невозможно изолировать для consent withdrawal.
2. **Audit (VR.R.002):** каждая запись должна иметь чёткий writer-trail. Implicit derivation = chain LLM-вызовов без атомарного writer-event.
3. **Capture-flow** (DP.SC.031/032): agent-write требует explicit user acceptance. Unified peer pattern размывает границу agent-write vs user-decl.

Honcho-pattern уместен для агентов без compliance-обязательств; для платформы развития интеллекта со взрослым consent-контуром — writer+owner расщепление обязательно.

### 2.2 SoTA по слоям

**Персона (user-declared):**
- Personal.ai (2025-2026): AI digital twins для PKM — реплицируют индивидуальную перспективу владельца.
- Lenovo Qira (CES 2026): «Fused Knowledge Base» — cross-device декларация контекста пользователя.

**Память (platform-observed):**
- Event Sourcing (Fowler 2005, Young 2010): append-only log + projection — архитектурная база Память.Observed + Derived.
- BKT / HLR как индустриальный стандарт для Derived-проекций обучения.

**Проекция (runtime):**
- CQRS read-models (Young 2010, Vernon 2016): отдельные read-модели под потребителя.
- Open Learner Model (Bull & Kay 2007; Brusilovsky overlay model 1996+): проекция под пользователя как учебный инструмент. У OLM literature чёткое разделение: **Learner Profile** (declarative, user-controlled — коррелят нашей Персоны) vs **Learner Model** (computed by system — коррелят нашей Памяти.Derived) vs **Open Learner Model view** (визуализация для пользователя — коррелят нашей Проекции). Это даёт международный академический glossary якорь для трёхслойного критерия.
- Context Engineering (DP.SOTA.002): runtime-сборка LLM-контекста — прямой предок LLM Context Projection.

### Критический взгляд на метафору «digital twin» для людей

- **Springer AI&Society 2026 «Personalised LLMs and the risks of the digital twin metaphor»** (link.springer.com/article/10.1007/s00146-026-02875-4) — критикует сам термин «twin» применительно к LLM-моделям человека. Аргумент: LLM-модель — не копия и не симуляция; это performative artefact, имитирующий человека на distribution train-данных, но без каузальной связи с оригиналом. Метафора «twin» вводит в заблуждение про epistemic status.
- **Springer Synthese 2026 «The ontology of the digital twin: contemporary case for metaontological analysis»** — DT синтезирует атрибуты, традиционно разделённые между онтологическими категориями (abstract/concrete, particular/universal), что требует пересмотра.

**Импликация для IWE:** мы используем «Цифровой двойник» (PD.ARCH.001 §3.3) для описания «модели себя для рефлексии». При следующей ревизии этого термина — пересмотреть в свете academic critique. Текущий приоритет — не переименование (см. peer-сессия 2026-05-31-11 verdict), а явное указание границ метафоры.

### Customer/UX дискурс — отдельная семантика «Persona»

В customer experience и UX-литературе термин **Persona** имеет другое значение: humanized fictional **типизация сегмента** (e.g. «Алиса, 28 лет, продакт-менеджер») для empathy/ideation, не для предсказания поведения. **Digital Twin** = real-time симуляция конкретного индивида. **Synthetic User** = generic репрезентация population segment. (Источники: doppeliq.ai, aiforinsightsleaders.substack.com 2025-2026.)

Наша «Персона» (индивидуальная декларация) формально ближе к «Digital Twin» этого дискурса, чем к их «Persona». Это известный collision — адресуется явным glossary entry в DP.D.052 §10 и DP.ARCH.005 §0.

## 3. SPF-интеграция

Экзокортекс IWE — ранняя реализация этого тренда с явным writer+owner расщеплением:

| Слой | Writer | Owner / Identity-anchor | Экзокортекс-реализация |
|------|--------|-------------------------|------------------------|
| **Персона** | Пользователь | Distributed: Ory `subject_id` (anchor) + Git пользователя (declarations) + Neon `persona_grants` (refs) — см. §1, §2.1 | PACK-personal + DS-my-strategy + CLAUDE.md + extensions |
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

- Letta (ex-MemGPT, Berkeley) — persona block (агент) / human block (user) / archival / recall, 2023+ — docs.letta.com
- Mem0 — structured user attributes vs raw memories, 2024-2025 — mem0.ai, arxiv 2504.19413
- LangMem (LangChain) — semantic/episodic/procedural, 2024+
- Anthropic Memory tool (Claude Files) — user-editable blocks, 2026 — docs.claude.com
- Honcho (Plastic Labs) — peer-based unified pattern, ingest/dream-time pipeline, 2024-2026 — honcho.dev, github.com/plastic-labs/honcho
- Fowler M. «Event Sourcing» — 2005
- Young G. «CQRS and Event Sourcing» — 2010
- Bull S., Kay J. «Student Models that Invite the Learner In: The SMILI Open Learner Modelling Framework» — IJAIED 17(2), 2007 (Learner Profile vs Learner Model vs OLM view); и направление research 2007+
- Brusilovsky P. — overlay user models в адаптивных учебных системах, 1996+ (e.g. «Methods and Techniques of Adaptive Hypermedia», UMUAI 6, 1996)
- Personal.ai. «AI Digital Twins: Future of PKM» (2025)
- Lenovo Qira. «Fused Knowledge Base» (CES 2026)
- arxiv.org/abs/2601.01321 — «Digital Twin AI: Opportunities and Challenges from LLMs to World Models» (2026) — §5
- Cameron K. «Laws of Identity» — 2005 (federated identity pattern)
- W3C Decentralized Identifiers (DID) Core 1.0 — W3C Recommendation, 19 июля 2022 — <https://www.w3.org/TR/did-1.0/> (distributed identity anchor)
- OAuth 2.0 RFC 6749 (2012), Dynamic Client Registration RFC 7591 (2015) — federated identity provider pattern
- Ory «decoupled identity» — <https://www.ory.sh/docs/concepts/identity>
- Solid / W3C user pod (Berners-Lee, ongoing) — solidproject.org — writer+owner split на уровне веб-стандарта
- Springer AI&Society 2026 — «Personalised LLMs and the risks of the digital twin metaphor» — link.springer.com/article/10.1007/s00146-026-02875-4 — critique of «twin» metaphor
- Springer Synthese 2026 — «The ontology of the digital twin: contemporary case for metaontological analysis» — link.springer.com/article/10.1007/s11229-026-05581-2

---

*Пересмотрено в WP-257 Ф5 (расщепление ЦД на Персона/Память/Проекция, 2026-04-22).*
*v2 уточнения 2026-05-31 (WP-214 фаза, peer-сессия 2026-05-31-11): точное описание Letta (persona block = агент, human block = user); Honcho как отвергаемая альтернатива (unified peer pattern); OLM-источники (Bull&Kay, Brusilovsky) как академический glossary якорь; добавлены Solid/DID/OAuth2 как опора distributed identity; добавлен раздел «Customer/UX дискурс» (collision с типизацией сегмента) и Springer 2026 critique.*
