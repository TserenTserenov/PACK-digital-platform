---
id: DP.M.369
name: "DRR Adequacy Pass"
name_ru: "Пас операционализации решения (DRR Adequacy Pass)"
name_en: "Decision readiness review adequacy pass — 6-question operationalization checklist"
summary: "6-вопросный чеклист ArchGate шаг 4.6: проверяет, что архитектурное/протокольное/продуктовое решение операционализировано, а не только записано. ≥1 ❌ → вернуться к детализации."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: architecture-review
valid_from: 2026-07-02
related:
  see_also: [DP.FM.010]
tags: [archgate, drr, operationalization, architecture-review, checklist, decision-readiness]
source: "session-close 2026-07-02, WP-448 Ф3 (commit 31c64be, .claude/skills/archgate/SKILL.md + .claude/templates/drr-template.md)"
schema_version: 1
---

# DP.M.369 — Пас операционализации решения (DRR Adequacy Pass)

## Описание

Шаг 4.6 ArchGate: после NBR-анализа и перед финальным вердиктом — проверить, что решение операционализировано, а не только сформулировано. Расширяет различение «Детектор существует ≠ детектор блокирует» — даёт конкретный алгоритм (6 вопросов) вместо одиночного флага.

## Algorithm

### Шаг 4.6: DRR Adequacy Pass

| # | Вопрос | ✅ / ⚠️ / ❌ |
|---|--------|------------|
| 1 | Решение операционализировано: есть чеклист/тест/инвариант, а не только принцип? | |
| 2 | Детектор/гейт реально блокирует, а не только существует в документе? | |
| 3 | Есть forcing function или explicit acknowledgement пользователя? | |
| 4 | Приёмка проведена (verify-pass/smoke/canary/acceptance report)? | |
| 5 | Запись ведётся в одном месте (OwnerIntegrity, нет дублей)? | |
| 6 | Указан срок/условие пересмотра? | |

### Интерпретация результата

- **≥1 ❌** → решение не готово к финальному вердикту; вернуться к шагу 2 с детализацией операционализации
- **≥3 ⚠️** → финальный вердикт возможен с обязательным ⚠️-флагом и explicit acknowledgement
- **Все ✅ / ⚠️ < 3** → финальный вердикт доступен

## Применимость

Любое архитектурное, протокольное или продуктовое решение при ArchGate или аналогичном review.
