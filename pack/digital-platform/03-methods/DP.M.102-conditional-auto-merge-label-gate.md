---
id: DP.M.102
name: Условный автоматический merge через метки PR и CI-гейт
name_ru: Conditional Auto-Merge via PR Labels
name_en: Conditional Auto-Merge via PR Labels and CI Gate
type: method
status: active
summary: "PR с разрешённой меткой (hotfix, pilot-approved) + все CI-чеки зелёные → автоматический merge. Создаёт ускоренную полосу для срочных исправлений без обхода CI. Граница безопасности: только разрешённые labels + CI pass обязателен."
created: 2026-05-19
trust:
  F: 3
  G: domain
  R: 0.80
epistemic_stage: validated
related:
  uses: []
  references: [DP.M.101]
tags: [automation, merge, ci, pull-request, hotfix, safety-gate, label]
wp: WP-322
---

# Conditional Auto-Merge via PR Labels (DP.M.102)

## 1. Контекст

Репо с несколькими контрибьюторами: срочные исправления (hotfix) ждут ручного merge от maintainer. Задержка создаёт риск. Обходить CI — не вариант.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость доставки hotfix ↔ гарантия CI-проверки | Метод открывает ускоренную полосу для срочных исправлений (§1: задержка ручного merge — риск), но удерживает жёсткое условие «все CI-чеки success»: hotfix = срочность, а не обход проверки (§4) |
| Автоматизация merge без maintainer ↔ защита от случайного слияния | Auto-merge срабатывает без человека, но только при явном разрешённом label (`hotfix`, `pilot-approved`) и опциональных ограничениях по авторам/ветвям; squash merge удерживает историю чистой |

## 2. Условия автоматического merge

- PR помечен разрешённым label (`hotfix`, `pilot-approved`, etc.)
- **Все** CI-чеки завершились со статусом success
- Дополнительно: можно ограничить по авторам или ветвям

## 3. Алгоритм

1. CI завершается → проверить labels на PR
2. Есть разрешённый label? → запустить auto-merge (squash)
3. CI fail? → не merge, уведомить автора

## 4. Граница безопасности

- **Не все PR** — только с явным label (предотвращает случайный merge)
- **CI-pass обязателен** — hotfix = срочность, не обход проверки
- **Squash merge** — история остаётся чистой

## 5. Применение

- Docs-as-code, content repos, curriculum репо
- Не применять к production-code без дополнительного review-гейта

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Скорость полосы затмевает настройку белого списка | Внимание смещается к «как быстро смержится hotfix» и недонастраивает список разрешённых labels и ограничения по авторам/ветвям (§2) — ускоренная полоса расширяется до «любой PR с меткой» |
| Настройка workflow затмевает наблюдение за дисциплиной меток | Внимание уходит в реализацию `auto-merge.yml`, а не в мониторинг того, не стал ли `pilot-approved` формальной кнопкой, которую вешают без чтения PR, — гейт существует, но перестаёт фильтровать |

## 6. Связи

- Смежный: DP.M.101 (Semver Automation) — часто срабатывает после auto-merge
- Реализация: `auto-merge.yml` в DS-principles-curriculum (WP-322 Ф15, commit c0de5b8)

---

> 2026-07-31 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 4). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
