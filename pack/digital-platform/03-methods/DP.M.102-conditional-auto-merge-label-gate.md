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

## 6. Связи

- Смежный: DP.M.101 (Semver Automation) — часто срабатывает после auto-merge
- Реализация: `auto-merge.yml` в DS-principles-curriculum (WP-322 Ф15, commit c0de5b8)
