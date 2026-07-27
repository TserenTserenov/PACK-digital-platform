---
id: DP.M.067
name: Two-pass review — subagent + self-revisit
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-316 commits 2b703f42 (переписка после subagent C-review) + 228b8ae6 (13 corrections self-pass) + d1e5465e (scope creep откат)
related:
  complements: [DP.M.061]
  applies_to: [ArchGate, IntegrationGate]
---

# DP.M.067: Two-pass review — subagent + self-revisit

## Определение

Независимый review длинного письменного deliverable (≥2h работы) — два прохода **разной природы**:

1. **Pass A — subagent C-review** (Claude в изолированном контексте, без памяти сессии).
2. **Pass B — self-revisit** (тот же автор, **через временной разрыв ≥1 час**).

Каждый проход обнаруживает класс ошибок, невидимый для другого.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Холодный взгляд subagent (Pass A, без памяти сессии) ↔ тёплый взгляд автора с temporal delta (Pass B, ≥1h спустя) | Subagent видит conceptual gaps и нарушения принципов, но не видит implementation drift и scope creep, которые видны только автору после временного разрыва; ни один pass в одиночку не покрывает оба класса ошибок (см. таблицу «Что ловит каждый pass») |
| Бюджет ≥2h deliverable ↔ стоимость обязательного разрыва ≥1 час перед Pass B | Тест применимости требует документ ≥2h работы, но соблюдение разрыва ≥1 час перед self-revisit — это дополнительная задержка close-цикла; сокращение разрыва экономит время, но лишает Pass B «temporal delta», которая и отличает его от простого повторного чтения |

## Что ловит каждый pass

| Pass | Природа взгляда | Типичные находки |
|------|----------------|------------------|
| **A — subagent** | Cold (нет контекста) | Conceptual gaps, архитектурные расхождения, нарушения принципов, противоречия в декларациях |
| **B — self-revisit** | Warm + temporal delta | Implementation details, ToC-consistency, ссылки-неактуальности, scope creep, мелкие inconsistencies |

## Тест применимости

> «Есть ли документ / план / спецификация ≥2 часа работы перед прод-применением?»

- Да → two-pass review обязателен.
- Нет → один pass достаточен (или review человеком).

## Антипаттерн

**Только subagent.** Пропускает implementation drift, scope creep, неактуальные ссылки — холодный взгляд не видит дельту от исходного замысла.

**Только self-revisit.** Пропускает conceptual blind spots — автор остаётся в своей когнитивной рамке.

## Прецедент

WP-316 Ф8 (Honcho-validation): (1) subagent C-review нашёл 5 контраргументов плана → переписка фаз (commit 2b703f42); (2) self-revisit через несколько часов нашёл ещё 13 корректировок (228b8ae6); (3) отдельный проход обнаружил scope creep → 4 corrective actions (d1e5465e).

## Различение

- **DP.M.061 (multi-round verifier):** проверяет spec↔impl alignment, сужая scope. Subject — соответствие двух уровней.
- **DP.M.062 (two-pass review):** проверяет внутреннюю целостность одного deliverable. Subject — сам документ. Природа различия — между взглядами (cold vs warm-temporal), не между scope.

Можно применять оба последовательно: DP.M.062 на этапе draft → DP.M.061 при готовности impl.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Pass B превращается в проверку замечаний Pass A | Вместо самостоятельного перечитывания deliverable спустя ≥1 час, внимание автора на Pass B съезжает к простой сверке «исправлены ли замечания subagent'а» — temporal-delta взгляд подменяется чек-листом по чужим находкам, и implementation drift/scope creep, которые ищет именно Pass B, остаются непойманными |
| Разрыв ≥1 час формально выдержан, но заполнен той же задачей | Временной интервал перед self-revisit соблюдается по часам, но внимание практикующего всё это время остаётся на том же документе (правки, соседние задачи по теме) — «остывания» контекста не происходит, и Pass B фактически превращается в Pass A.2, а не в независимый тёплый взгляд с дистанции |

## Источник

WP-316 Ф8 close, 2026-05-17. Расширяет авторскую практику ArchGate-review (см. feedback_archgate_independent_review.md в memory/) до Pack-уровня двухпроходной модели.

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
