---
id: DP.D.052
name: "Различение: Персона / Память / Контекст"
type: distinction
status: active
summary: "Три слоя пользовательской модели — замена legacy-термина «ЦД». Критерий разделения = writer + owner (source-of-truth), не когнитивный и не по TTL. Персона = user-owned Git, Память = platform-owned Neon, Контекст (= Проекция) = runtime-ephemeral."
created: 2026-04-22
updated: 2026-04-22
valid_from: 2026-04-22
trust:
  F: 4
  G: domain
  R: 0.85
related:
  distinguishes: [DP.ARCH.005, DP.ARCH.006, DP.ARCH.007]
  replaces_part_of: [DP.ARCH.003]
  used_by: [DP.SC.104, DP.CONCEPT.003, DP.D.035, DP.ARCH.004]
tags: [persona, memory, projection, user-model, writer-owner, distinction]
---

# DP.D.052 — Различение: Персона / Память / Контекст

> **Онтологическая замена «ЦД».** Монолит «цифровой двойник» (DP.ARCH.003) расщепляется на три сущности с разными writer и owner.

## 1. Зачем различать

До WP-257 платформа использовала термин «Цифровой двойник» (ЦД) как монолит. Под одним словом смешивались:
- то, что *пользователь декларирует о себе* (цели, preferences);
- то, что *платформа наблюдает* (события, агрегаты);
- то, что *агент собирает на лету* под запрос (LLM-контекст, view, nudge).

Смешение ломает ключевой инвариант **OwnerIntegrity**: один факт — одно место с одним владельцем. Writer'а у «ЦД» оказывалось много, и при сбое было непонятно, в какой слой смотреть.

**Решение:** разделить по **writer + owner** (source-of-truth).

## 2. Три слоя

| Слой | Writer | Owner | Пример хранимого | Подробно |
|------|--------|-------|------------------|----------|
| **Персона** | Пользователь (или агент по его поручению с acceptance) | Git пользователя | PACK-personal, DS-my-strategy, captures, preferences, Ory identity (декларация) | [DP.ARCH.005](../02-domain-entities/DP.ARCH.005-persona-entity.md) |
| **Память** | Платформа автоматически | Neon | Events (activity-hub), payments, baseline/potential/BKT/HLR, подписки | [DP.ARCH.006](../02-domain-entities/DP.ARCH.006-memory-record.md) |
| **Контекст** (= Проекция) | Агент в runtime | Не хранится долго | Промпт LLM, Open Learner Model view, nudge message | [DP.ARCH.007](../02-domain-entities/DP.ARCH.007-projection.md) |

> **Терминология (канон).** В архитектурном дискурсе используем **Проекция** (DP.ARCH.007) как каноническую сущность runtime-слоя. «Контекст» = обиходный синоним (LLM context window, промпт-сборка) — встречается в названии различения и тестах границы ради читаемости, но в спецификациях и кода ссылаемся на Проекцию. Путать не надо: это одно и то же, просто обиходное слово и архитектурное.

## 3. Тест границы

**Вопрос:** «Что пропадёт, если удалю X?»

| Удалить | Слой |
|---------|------|
| Git пользователя | → Персона пропала |
| Neon | → Память пропала |
| Прервать текущий LLM-вызов | → Проекция пропала |

## 4. Под-уровни Памяти

Память имеет два под-уровня (CQRS / Event Sourcing):

| Под-уровень | Writer | Природа | Пример |
|-------------|--------|---------|--------|
| **Observed** | Платформа (в момент события) | Append-only event log | test_answered, session_start, payment_succeeded |
| **Derived** | Платформа (в момент пересчёта) | Агрегат из Observed | skill_mastery (BKT), memory_decay (HLR), engagement, baseline/potential |

Событие ≠ состояние (HD Р-W17-1). Observed описывает момент; Derived — состояние к моменту.

## 5. Что НЕ входит в пользовательскую модель

Пять отдельных категорий (не слои пользовательской модели):

1. **Platform-knowledge** — PACK-digital-platform, PACK-MIM, ZP/FPF/SOTA (общая онтология команды, не принадлежит ни одному пользователю).
2. **Catalog / Reference** — тарифы, уровни квалификации, правила начисления баллов (платформенные справочники).
3. **Service / Ops** — Metabase (#7), health DB (#8), Ory sessions, Keto policies (операционные сервисы, не данные пользователя).
4. **Relational** — связи Persona ↔ Persona: наставничество, community (двусторонние связи, не принадлежат одной Персоне).
5. **Proto-Persona** (Pre-Grant) — лид до появления Ory identity; `subscription_grants` с `claim_token` до claim flow → превращается в полноценную Персону.

## 6. Перпендикулярные атрибуты (не слои)

Эти атрибуты ортогональны writer/owner и применимы к любому слою:

- **PII-класс** (public / PII / payment_credentials / secrets — см. DP.ARCH.004 §2 П6.1)
- **Projection vs stored** (вычисляется на лету vs хранится)
- **Temporal validity** (`valid_from`, `valid_until`, `superseded_by`)
- **Consent / GDPR** (какое согласие покрывает)

## 7. Edge cases

| Артефакт | Слой | Обоснование |
|----------|------|-------------|
| Ory identity (email, telegram_id) | Персона | Декларация пользователя, хоть и в Ory store |
| Bot-сообщение пользователя («запомни X») | Память.Observed (event) | Декларация, но входит в Память как наблюдение; далее возможно captures-flow → Персона |
| Bot FSM state (текущий шаг диалога) | Память.Observed (ephemeral) | Платформа пишет, но живёт коротко |
| Pack платформы (PACK-digital-platform) | Platform-knowledge (вне модели) | Не принадлежит пользователю |
| Pack пользователя (PACK-personal) | Персона | Принадлежит пользователю, в его Git |
| Knowledge-индексы personal-коллекций | Память / projection Персоны | Вычисляется платформой из Персоны; писатель — платформа |

## 8. SoTA-прецеденты (2025-2026)

- **Letta (Berkeley, MemGPT):** persona/human/archival/recall blocks — прямое соответствие Персона + Память.
- **Mem0:** structured user attributes vs raw memories — Персона vs Память.Observed.
- **LangMem (LangChain):** semantic (факты = Персона), episodic (события = Observed), procedural (паттерны = Derived).
- **Anthropic Memory tool (Claude Files):** user-editable memory blocks — поддержка Персоны.

## 9. Downstream

- **HD #27** (`exocortex/hard-distinctions.md`) — полная версия с таблицами и ролевыми контрактами.
- **WP-227** — `/twin` как сервис над тремя endpoints (persona / memory / projection), не монолит.
- **PACK-MIM** — контракты Диагноста / Портного / Оценщика: какие слои читают, какие пишут.
- **БД Neon (9 штук)** — пакетное переименование в **WP-228 Ф25** (после Д1-Д12 структурных правок).
- **DP.ARCH.003** расщеплён в **WP-257 Ф5** на DP.ARCH.005 + DP.ARCH.006 + DP.ARCH.007.

## 10. Связанные документы

- [DP.ARCH.005](../02-domain-entities/DP.ARCH.005-persona-entity.md) — Персона
- [DP.ARCH.006](../02-domain-entities/DP.ARCH.006-memory-record.md) — Память
- [DP.ARCH.007](../02-domain-entities/DP.ARCH.007-projection.md) — Проекция
- [DP.SC.104](../08-service-clauses/DP.SC.104-adaptive-personalization.md) — Адаптивная персонализация (обещание)
- [DP.CONCEPT.003](../02-domain-entities/DP.CONCEPT.003-adaptive-personalization.md) — Три механизма
- [DP.D.035](DP.D.035-data-policy.md) — Единая политика данных (агрегирует)

---

*Статус: active. Создано в WP-257 Ф5 (одновременно с расщеплением DP.ARCH.003).*
*Создано: 2026-04-22.*
