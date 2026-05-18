---
id: DP.D.075
name: "personal_search (семантический транспорт) ≠ Honcho (накопитель инференций)"
type: distinction
status: draft
summary: "personal_search — семантический доступ к источникам текста; Honcho — накопитель паттернов между запусками. В cognitive proxy pipeline: personal_search = транспорт, Honcho = память."
created: 2026-05-18
valid_from: 2026-05-18
sources:
  - WP-316 Ф8.2б (commit 7614f942, ревизия роли Honcho в cognitive proxy)
related:
  refines: [DP.D.050]
  references: [DP.SC.021, DP.M.085]
---

# DP.D.075 — personal_search ≠ Honcho в cognitive proxy pipeline

## Различение

**personal_search** — семантический транспорт: даёт доступ к источникам текста (клубные посты, LMS ДЗ, репо, руководства) через эмбеддинги. Уже индексирован, уже семантический.

**Honcho** — накопитель инференций: хранит паттерны, выведенные из прошлых циклов оценки, чтобы не переоценивать одно и то же между запусками. Не нужен как транспорт если personal_search уже покрывает источники.

## Пайплайн cognitive proxy

```
personal_search → Claude inference → cognitive schema
                     ↕ (между запусками)
                   Honcho (накопитель)
```

Honcho входит в контур только для накопления результатов инференции — не для доступа к источникам.

## Тест (разделения ролей)

«Есть ли уже семантический поиск по нужным источникам текста?»
- Да → Honcho как транспорт источников **избыточен**
- Нет → рассмотреть Honcho как транспорт или альтернативный индексатор

## Источники текста (приоритет)

1. DS-Knowledge-Index (клубные посты с эмбеддингами) — через personal_search
2. LMS ДЗ (Neon, read-only) — через personal_search
3. systemsworld.club posts — через personal_search
4. personal-guide — через personal_search

## Связь со смежным различением

Дополняет «Memory provider (Honcho-style) ≠ Digital Twin» (distinctions.md): тот — разница provider vs twin по оси точности. Этот — конкретная роль Honcho в cognitive proxy pipeline по оси транспорт vs накопитель.
