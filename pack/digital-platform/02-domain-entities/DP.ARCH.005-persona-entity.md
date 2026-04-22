---
id: DP.ARCH.005
name: Персона (декларативная модель созидателя)
type: domain-entity
status: approved
summary: "Персона — декларативный слой модели пользователя. Писатель = пользователь (или агент по его поручению с acceptance), владелец = Git-репо пользователя (PACK-personal, DS-my-strategy, captures). Платформа — read-only. Заменяет часть монолита ЦД (DP.ARCH.003)."
created: 2026-04-22
updated: 2026-04-22
valid_from: 2026-04-22
trust:
  F: 4
  G: domain
  R: 0.6
epistemic_stage: emerging
related:
  specializes: [U.System]
  uses: [DP.ARCH.001, DP.D.052]
  used_by: [DP.ARCH.006, DP.ARCH.007, DP.SC.104, DP.CONCEPT.003]
  replaces_part_of: [DP.ARCH.003]
tags: [persona, user-model, declarative, git-owned]
---

# Персона (декларативная модель созидателя)

> **Расщепление ЦД (WP-257 Ф5).** Монолит DP.ARCH.003 «Архитектура цифрового двойника» распался на три сущности по критерию writer + owner (DP.D.052):
> - **Персона** (этот файл) — writer = пользователь, owner = Git
> - **Память** ([DP.ARCH.006](DP.ARCH.006-memory-record.md)) — writer = платформа runtime, owner = Neon
> - **Проекция** ([DP.ARCH.007](DP.ARCH.007-projection.md)) — writer = агент в runtime, owner = ephemeral

## 1. Назначение

Персона — **декларативная модель пользователя**, которую создаёт и владеет сам пользователь. Это то, что человек **говорит о себе**: цели, ценности, роли, предпочтения, ограничения, контекст.

**Ключевой принцип writer + owner:** пользователь — единственный writer Персоны. Платформа читает Персону, но не пишет в неё. Агент может редактировать Персону **только по поручению пользователя** (capture flow → acceptance).

**Критерий различения от Памяти** (DP.D.052):
- Персона: «Я говорю о себе» (декларация)
- Память.Observed: «платформа наблюдает, что я делал» (событие)
- Память.Derived: «платформа вычислила из моих действий» (агрегат)

## 2. Что содержит Персона

| Категория | Что это | Где живёт |
|-----------|---------|-----------|
| **Identity** | Имя, контакты, идентификаторы (Ory UUID, Telegram ID) | Ory identity store (read) + Git user config |
| **Цели и намерения** | Целевые системы, рабочие продукты, программа развития | `PACK-personal/personal-development/` |
| **Ценности** | Миссия, приоритеты, критерии | `PACK-personal/` |
| **Предпочтения** | Язык, каналы, ритм, стиль подачи | `CLAUDE.md`, `extensions/` |
| **Контекст** | Текущие РП, stakeholder map, окружение | `DS-my-strategy/` |
| **Captures / Fleeting notes** | Сырые заметки до структурирования | `DS-my-strategy/inbox/captures.md` |
| **Self-assessment** | Самооценка навыков, стадии, ограничений | Через capture → `PACK-personal/` |
| **Proto-Persona** (Pre-Grant) | Лид до появления Ory identity | `subscription_grants` (Neon, до claim flow) |

## 3. Writer + Owner контракт

| Атрибут | Значение |
|---------|----------|
| **Writer** | Пользователь (через Git commit, бота с capture-flow + acceptance, MCP `personal_write` с auth) |
| **Writer exceptions** | Агент по явному поручению пользователя (например, `personal_propose_capture` → пользователь утверждает → commit) |
| **Owner** | Git-репозиторий пользователя (`PACK-personal`, `DS-my-strategy`, `DS-MCP-configs`) |
| **Reader** | Платформа (MCP `personal_search`, `knowledge_get_document`, Portnoy/Tailor), агенты (Стратег, Консультант) |
| **Write API** | Git commit (primary), MCP `personal_write` / `personal_propose_capture` (secondary with acceptance) |
| **Persistence** | Git history (append + mutation через rebase/squash — пользователь распоряжается) |
| **Portability** | `git clone` — пользователь уносит полную Персону при уходе |

## 4. Граница Персоны

> **Тест границы** (DP.D.052): «Что пропадёт, если удалю X? Git пользователя → Персона. Neon → Память. Прервать LLM-вызов → Контекст.»

**Внутри Персоны:**
- PACK-personal Pack со всеми Pack-сущностями пользователя (цели, системы, характеристики, методы)
- DS-my-strategy стратегический хаб (РП, планы, сессии, решения)
- Captures и fleeting notes (сырой входящий поток)
- Настройки экзокортекса (CLAUDE.md, extensions, hooks)
- Ory identity декларация (имя, email — declarative facts)

**Вне Персоны** (→ Память):
- Сессии в боте (session_start/end events)
- Ответы на тесты (test_answered events)
- Чаты с ИИ (ai_chat events)
- Транзакции (payment events)
- Вычисленные агрегаты (BKT P(known), HLR half-life, stage, potential, baseline)

**Вне Персоны** (→ Проекция):
- Промпт, собранный для одного LLM-вызова
- Рекомендация, сгенерированная на лету
- Open Learner Model view (текущий рендер)

## 5. Роли, работающие с Персоной

| Роль | Что делает с Персоной | Границы |
|------|---------------------|---------|
| **Пользователь** | Пишет, редактирует, версионирует | Полные права |
| **Стратег (R1, DP.ROLE.012)** | Читает для стратегирования, предлагает правки через Git PR | Write только через pull-request |
| **Портной (DP.ROLE.021)** | Читает для персонализации контента | Read-only |
| **Consultant / Tutor** | Читает для адаптации объяснений | Read-only |
| **Capture Agent** | Предлагает новые знания из чата пользователя | Write через acceptance flow |

## 6. Связь с адаптивной персонализацией (DP.CONCEPT.003)

Персона реализует механизм **Индивидуализации** (2-й из 3-х механизмов DP.CONCEPT.003):

| Механизм | Слой | Кто делает | Как |
|---|---|---|---|
| Персонализация | Память.Derived + Проекция | Платформа (Портной) | Читает Derived → собирает Context → генерирует контент |
| **Индивидуализация** | **Персона** | **Пользователь** | **Настраивает IWE под себя: Pack, extensions, preferences** |
| Адаптивность | Память (Observed → Derived) | Система (BKT/HLR) | Обновляет Derived по обратной связи |

## 7. Инварианты

1. **Single writer:** только пользователь (или агент по его явному поручению) пишет в Персону. Нарушение = утечка owner integrity.
2. **Portable:** `git clone` пользовательских репо — полная Персона уносится без зависимости от платформы.
3. **Platform read-only:** платформа не пишет в Персону напрямую. Propose → accept → user commit.
4. **Consent required:** agent-proposed changes требуют явного acceptance (например, `/apply-captures`).
5. **Version history:** Git log хранит эволюцию Персоны.

## 8. SoTA-основа (2025-2026)

- **Letta (Berkeley, MemGPT):** persona/human blocks — именованный прецедент разделения декларативной Персоны и архивной Памяти
- **Anthropic Memory tool (Claude Files):** user-editable memory blocks
- **LangMem (LangChain):** semantic/episodic/procedural — Persona ≈ semantic (facts about user)
- **Mem0:** structured user attributes vs raw memories — маппинг на Persona vs Memory

## 9. Связанные документы

- [DP.D.052](../01-domain-contract/DP.D.052-persona-memory-context.md) — различение Персона/Память/Контекст
- [DP.ARCH.006](DP.ARCH.006-memory-record.md) — Память (platform-owned observed + derived)
- [DP.ARCH.007](DP.ARCH.007-projection.md) — Проекция (runtime compilation)
- [DP.SC.104](../08-service-clauses/DP.SC.104-adaptive-personalization.md) — Адаптивная персонализация
- [DP.CONCEPT.003](DP.CONCEPT.003-adaptive-personalization.md) — 3 механизма персонализации
- [PACK-personal](../../../../PACK-personal/) — физический носитель Персоны

---

*Статус: approved. Создано в WP-257 Ф5 (расщепление DP.ARCH.003 по writer+owner критерию).*
*Создано: 2026-04-22.*
