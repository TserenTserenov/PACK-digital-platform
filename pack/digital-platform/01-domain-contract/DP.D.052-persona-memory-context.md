---
id: DP.D.052
name: "Различение: Персона / Память / Контекст"
type: distinction
status: active
summary: "Три слоя пользовательской модели — замена legacy-термина «ЦД». Критерий разделения = writer + owner (source-of-truth), не когнитивный и не по TTL. Персона = distributed-entity (identity-anchor + Git declarations + Neon refs), Память = platform-owned Neon, Контекст (= Проекция) = runtime-ephemeral. v2: разделены оси Writer / Identity-anchor / State-storage / Snapshot-unit (вместо склейки Owner+Артефакт); добавлено различение Носитель ≠ Персона ≠ Декларация Персоны (§10)."
created: 2026-04-22
updated: 2026-05-31
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

## 2. Три слоя — четыре оси (v2, 2026-05-31)

> **Изменение по сравнению с v1 (22 апр).** Предыдущая таблица склеивала «Owner» и «Артефакт» в одну колонку, что приводило к concept confusion: читатель видел «Owner = Git пользователя» и заключал, что Персона = файл в Git. На самом деле Персона — entity с **distributed identity**: identity-anchor (Ory subject_id или Pre-Grant claim_token) живёт отдельно от deklarативного содержимого (Git) и от ссылок (Neon). Распутываем оси.

| Слой | Writer | Identity-anchor | State-storage | Snapshot-unit | Подробно |
|------|--------|-----------------|---------------|---------------|----------|
| **Персона** | Пользователь (или агент по его поручению с acceptance) | `Ory subject_id` (UUID, immutable; для Pre-Grant — `subscription_grants.claim_token`) | Git пользователя (PACK-personal, DS-my-strategy, captures, preferences) + ссылки в Neon (`persona_grants.ory_identity_id`) | Git commit hash + frontmatter `valid_from` декларации | [DP.ARCH.005](../02-domain-entities/DP.ARCH.005-persona-entity.md) |
| **Память** | Платформа автоматически | `Ory subject_id` (FK через `persona_grants`) | Neon (Observed events + Derived aggregates) | Event log offset + Derived snapshot version | [DP.ARCH.006](../02-domain-entities/DP.ARCH.006-memory-record.md) |
| **Контекст** (= Проекция) | Агент в runtime | `Ory subject_id` (read для адресации) | Эфемерно (память процесса) | LLM-prompt assembly id (живёт от запроса до ответа) | [DP.ARCH.007](../02-domain-entities/DP.ARCH.007-projection.md) |

> **Терминология (канон).** В архитектурном дискурсе используем **Проекция** (DP.ARCH.007) как каноническую сущность runtime-слоя. «Контекст» = обиходный синоним (LLM context window, промпт-сборка) — встречается в названии различения и тестах границы ради читаемости, но в спецификациях и кода ссылаемся на Проекцию. Путать не надо: это одно и то же, просто обиходное слово и архитектурное.

> **Distributed identity pattern.** Identity-anchor Персоны живёт в одном месте (Ory или Pre-Grant store), а state-storage — в другом (Git + Neon refs). Это стандартный паттерн federated identity систем (см. Cameron «Laws of Identity» 2005; OAuth2 federated identity RFC 6749 / 7591; W3C Decentralized Identifiers 2022) и архитектурно совпадает с «decoupled identity» Ory (<https://www.ory.sh/docs/concepts/identity>). Централизованная таблица `personas` в Neon была бы дублированием Ory subject_id и нарушала OwnerIntegrity — отвергаем.

## 3. Тест границы (v2 — по новым осям)

**Вопрос:** «Что пропадёт, если удалю X?» — теперь проверяется по каждой оси отдельно.

| Удалить | Что пропадёт | Что останется |
|---------|--------------|---------------|
| Identity-anchor Персоны (Ory subject_id или claim_token) | Identity Персоны → Персона перестаёт существовать как entity (её декларации становятся orphan-снимками) | Файлы в Git как orphan-history; Neon events с разорванной FK |
| Git пользователя (PACK-personal/ etc.) | Все декларации Персоны (содержимое) | Identity-anchor в Ory; ссылки и наблюдения в Neon (Персона = entity жива, но без декларативного содержимого) |
| Один Git commit | Одна snapshot-версия декларации | Identity, остальные снимки в Git history, Neon refs |
| Neon `persona_grants` записи | Связь Персоны с Памятью (refs) | Identity-anchor + декларации в Git (Персона жива, но Память «отвязана») |
| Neon (целиком) | Память пропала | Персона (anchor + Git) цела |
| Прервать текущий LLM-вызов | Проекция пропала | Персона + Память целы |
| Носитель (человек в физическом мире) перестал заходить | Ничего внутри платформы не пропадает | Всё (платформа не имеет прямого доступа к носителю; см. §11) |

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
- **Google opensource memory layer (2026-04):** SQLite + LLM-консолидация полезного контекста во времени вместо vector-DB stack. Близко к Mem0 (structured attributes), отличается storage backend; заявленное преимущество — low-cost long-term memory без отдельной vector-DB инфраструктуры. Прецедент в пользу JSONB/SQLite-подхода для MVP вместо ранней миграции на vector-DB вендора.

## 9. Downstream

- **HD #27** (`exocortex/hard-distinctions.md`) — полная версия с таблицами и ролевыми контрактами.
- **WP-227** — `/twin` как сервис над тремя endpoints (persona / memory / projection), не монолит.
- **PACK-MIM** — контракты Диагноста / Портного / Оценщика: какие слои читают, какие пишут.
- **БД Neon (9 штук)** — пакетное переименование в **WP-228 Ф25** (после Д1-Д12 структурных правок).
- **DP.ARCH.003** расщеплён в **WP-257 Ф5** на DP.ARCH.005 + DP.ARCH.006 + DP.ARCH.007.

## 10. Носитель ≠ Персона ≠ Декларация Персоны (v2, 2026-05-31)

> **Производное различение, делает явной ось «сущность vs снимок» внутри Персоны.** Введено по итогам ревизии онтологии (peer-сессия 2026-05-31-11). Адресует пилотскую интуицию «в БД хранится не Персона, а её Версия»: интуиция верна (Git хранит snapshots, не саму entity), но не требует переименования — требует явного разделения трёх сущностей.

| Сущность | Что это | Где живёт | Кто writer |
|----------|---------|-----------|------------|
| **Носитель** | Человек в физическом мире | Вне платформы | — (платформа не пишет в носителя) |
| **Персона** | Entity-в-IWE, представляющая одного носителя | Distributed composition: Ory subject_id (anchor) + Git PACK-personal (declarations) + Neon `persona_grants` (refs) | Пользователь (для declarations) + Платформа (для refs); identity-anchor издаётся системой регистрации |
| **Декларация Персоны** (Persona Snapshot) | Один срез декларативного содержимого Персоны на момент T | Git commit + frontmatter `valid_from` | Пользователь (или агент по поручению с acceptance) |

**Тесты различения:**

1. *Носитель vs Персона.* «Если носитель перестанет заходить — пропадёт ли Персона?» — Нет, Персона остаётся как entity внутри платформы (anchor живёт в Ory, декларации в Git, история наблюдений в Neon). Это и есть причина асимметрии: Персона — артефакт системы, носитель — нет.

2. *Персона vs Декларация Персоны.* «Если удалить один Git commit (одну версию snapshot.md) — пропадёт ли Персона?» — Нет, Персона жива (anchor + остальные commits + Neon refs). Пропадёт одна версия декларации.

3. *Полная смерть Персоны.* «Что нужно удалить, чтобы Персона перестала существовать?» — Identity-anchor (Ory subject_id или Pre-Grant claim_token). Удаление содержимого Git или Neon записей оставляет Персону как «entity без декларации» или «entity без памяти», но не уничтожает её.

**Lifecycle anchor (важная деталь):**
Persona anchor проходит две фазы. Pre-Grant: лид/гость, ещё нет Ory identity — anchor = `subscription_grants.claim_token`. Granted (после claim flow): полноценная Персона — anchor = `Ory subject_id`. Переход односторонний и атомарный. На обеих фазах Персона — валидная entity, но с разной prov-историей. Подробнее → DP.ARCH.005 §0.

**Что это меняет в практике:**
- При обсуждении «хранилища Персоны» — указывать какой именно компонент имеется в виду (anchor / declarations / refs), не «вся Персона в Git».
- При проектировании удалений (GDPR right-to-be-forgotten) — последовательность: revoke anchor → wipe Git → wipe Neon refs; одно из трёх — частичная смерть, не полная.
- При импорте Letta/Mem0-терминологии — наш «Persona» = их `human block` (Letta) или `structured user attributes` (Mem0), НЕ их `persona block` (= self-описание агента в Letta).

## 11. Связанные документы

- [DP.ARCH.005](../02-domain-entities/DP.ARCH.005-persona-entity.md) — Персона
- [DP.ARCH.006](../02-domain-entities/DP.ARCH.006-memory-record.md) — Память
- [DP.ARCH.007](../02-domain-entities/DP.ARCH.007-projection.md) — Проекция
- [DP.SC.104](../08-service-clauses/DP.SC.104-adaptive-personalization.md) — Адаптивная персонализация (обещание)
- [DP.CONCEPT.003](../02-domain-entities/DP.CONCEPT.003-adaptive-personalization.md) — Три механизма
- [DP.D.035](DP.D.035-data-policy.md) — Единая политика данных (агрегирует)

---

*Статус: active. Создано в WP-257 Ф5 (одновременно с расщеплением DP.ARCH.003).*
*Создано: 2026-04-22.*
