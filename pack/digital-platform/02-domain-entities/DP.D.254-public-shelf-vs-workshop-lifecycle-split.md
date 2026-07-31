---
id: DP.D.254
name: "PublicShelf (только published) ≠ Workshop (черновики + мастерская) — split контент-репо по lifecycle-статусу"
name_ru: "Публичная полка ≠ Мастерская — разделение репо контент-конвейера по lifecycle-статусу артефакта"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-28
created: 2026-07-18
source: "git diff DS-my-strategy commits 6444c9c27, a4dfbaf80; WP-442 Ф11 session record 2026-06-26; extraction-report 2026-06-28-inbox-check #2"
see_also: [DP.D.110]
schema_version: 1
---

# DP.D.254 PublicShelf ≠ Workshop: split контент-репо по lifecycle-статусу

| Аспект | PublicShelf (DS-Knowledge-Index) | Workshop (DS-Tseren-Brand) |
|--------|----------------------------------|---------------------------|
| **Содержимое** | Только status:ready и status:published | Черновики, style/, topic-log, аналитика, видео-идеи |
| **Читает autopublisher** | Да — единственный источник | Нет |
| **Нужна фильтрация при чтении** | Нет (всё ready/published) | Да — при выборочном использовании |
| **Риск случайной публикации черновика** | Исключён структурно | Требует явного контроля статуса |

## Инвариант

Autopublisher сканирует только PublicShelf. Любой артефакт без `status:ready` или `status:published` — только в Workshop. Граница split определяется полем `status` артефакта, а не темой или типом контента.

## Применение

**Сигнал к split:** если autopublisher вынужден фильтровать статус *внутри* репо — репо смешан, нужен split. После split: autopublisher читает один источник целиком без фильтрации.

**При добавлении нового инструмента автоматизации** контент-конвейера: сначала определить — он читает готовый (→ PublicShelf) или обрабатывает любой (→ Workshop)?

## Тест

«Может ли черновик случайно попасть в публикацию без явной смены репо?» Да → репо смешан. Нет → split работает.

## Связи

- WP-442 Ф11 — источник паттерна (разделение DS-Knowledge-Index-Tseren и DS-Tseren-Brand)
- DP.D.110 (pillar-text ≠ conversion-post) — ортогональное различение по типу, а не по lifecycle
