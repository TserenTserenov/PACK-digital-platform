---
id: DP.D.069
name: Documentation-WP ≠ Implementation-WP — paired related-WPs, не один РП
kind: Distinction
status: active
created: 2026-05-17
sources:
  - "session-transcript 2026-05-17 17:24 UTC (pilot decision: РП246 = документация по Архитектуре, РП327 = реализация системы → парные related-WP)"
  - captures.md 2026-05-17 session-close-feed (WP-326 close)
related:
  contrasts_with:
    - DP.D.064-same-vs-different-promise-wp-branch  # D.064 — фаза vs новый РП внутри одного обещания; D.071 — два РП для одной фичи с разной природой deliverable
    - DP.D.066-blueprint-vs-build  # D.066 — замысел vs реализация одного артефакта; D.071 — два связанных артефакта (doc + impl) для одной фичи
  complements:
    - feedback_fixes_as_phases_not_separate_wp.md  # in-scope фиксы — фазы того же РП; D.071 — две природы deliverable требуют двух РП
---

# DP.D.071: Documentation-WP ≠ Implementation-WP — paired related-WPs, не один РП

## Различение

Документация архитектуры (Pack-формализация: SC + Role + FM + методы) и реализация системы (код, SQL-миграции, deploy, smoke-тесты) — **разные work-products с разной природой deliverable и разными critреria of done**. Объединять их в один РП — анти-паттерн.

| Аспект | Documentation-WP | Implementation-WP |
|--------|------------------|-------------------|
| **Природа deliverable** | Текст в Pack-репо (новые DP.SC.NNN / DP.ROLE.NNN / DP.FM.NNN / DP.M.NNN) | Код в DS-репо + миграции + deploy + smoke |
| **Класс верификации** | open-loop или problem-framing (VR.R.001 reads/grep + Pack-coherence) | closed-loop (smoke-test, integration test, прод-метрика) |
| **DoD** | Pack-coherence: SC ссылается на Role, Role ссылается на FM, MAP обновлён | Сервис запущен, smoke зелёный, прод-метрика в норме |
| **Репо назначения** | `PACK-*` | `DS-*` (governance + код) |
| **Бюджет** | обычно 1–4ч (формализация знания) | обычно 4–20ч (код + тесты + deploy) |
| **Re-open** | при изменении Pack-структуры (новый distinction, FM) | при production-инциденте или новой фазе функциональности |

## Тест применимости

> «Требуется ли для этой фичи формализация в Pack (новый SC/Role/FM/Method) И реализация в коде?»

- **Да → 2 связанных РП**, не один: doc-WP + impl-WP с bidirectional `related: [WP-X]` в frontmatter обоих, оба упоминаются в обоих контекстах.
- **Нет** (либо только формализация, либо только код существующего обещания) → один РП.

## Антипаттерн (объединение в один РП)

1. **Verification-class mismatch:** один РП не может иметь два DoD — open-loop по Pack и closed-loop по smoke. Один из них в итоге пропускают.
2. **Deliverables в разных репо:** scope разрывается, push-история фрагментирована, чек-листы Close-протокола не сходятся.
3. **Бюджеты сильно разные:** оценка ×3 искажена, нет внятной точки переноса в backlog.
4. **Re-open для одной части без другой не работает:** баг в коде ≠ refactor Pack-структуры; объединённый РП теряет capability re-open под конкретный класс.

## Прецедент

WP-246 (документация архитектуры рассрочки / Pack-формализация Service Clause + Role) и WP-327 (реализация системы рассрочки: код в боте + SQL + deploy). Пилот 17 мая принял решение: парные РП с bidirectional `related`, обе ведутся параллельно, но в разных сессиях. Тест: РП-246 закрывается VR.R.001 (Pack-coherence по SC + Role); РП-327 закрывается smoke-test (charge + refund + edge-cases). Разные DoD, разные исполнители, общая фича.

## Применимость

- Новые сервисы платформы (новый SC + новый код).
- Новые роли с реализующим воркером (DP.ROLE.NNN + сам воркер).
- Новые методы с инструментом-агентом (DP.METHOD.NNN + сам инструмент).
- Расширение существующего сервиса с новым обещанием (новый SC + код для него).

## Связи

- DP.D.064 — «То же обещание ≠ Другое обещание» решает «фаза или новый РП внутри одной фичи»; D.071 решает «один или два РП для одной фичи разной природы».
- DP.D.066 — «Blueprint ≠ Build» — замысел vs реализация одного артефакта; D.071 — два связанных артефакта (doc + impl) для одной фичи.
- `feedback_fixes_as_phases_not_separate_wp.md` — in-scope фиксы = фазы того же РП; D.071 — две природы deliverable требуют двух РП с самого начала, не разделение пост-фактум.
