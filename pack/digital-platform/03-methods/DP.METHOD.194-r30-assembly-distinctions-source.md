---
id: DP.METHOD.194
name: "R30 Assembly/Hybrid: различение-кандидаты — только из собственных SoTA-источников Pack"
type: method
pack: PACK-digital-platform
domain: digital-platform / pack-assembly
kind: Method
status: active
created: 2026-07-15
trust: confirmed
epistemic_stage: confirmed
valid_from: 2026-07-10
sources:
  - "SPF commit 563f18c (spec/process/r30-assembly-hybrid.md, WP-474 Ф6) + peer-session 2026-07-10-18"
related:
  see_also:
    - "SPF/spec/process/r30-assembly-hybrid.md — source spec"
schema_version: 1
---

# DP.METHOD.194 — R30 Assembly/Hybrid: различение-кандидаты только из собственных SoTA-источников Pack

## Правило

При R30 (Assembly/Hybrid): кандидаты на различения берутся из **собственных SoTA-источников Pack** (директория `06-sota/`), объявленных в `sota_sources`.

**Соседние Pack'и:** только форма (структура) — **никогда содержание**.

Gated fallback: если `sota_sources: none` → стандартные Base SoTA.

## IPO

- **Вход:** процесс R30 Assembly/Hybrid с доступом к нескольким Pack'ам
- **Процесс:** при поиске различение-кандидатов → искать только в `06-sota/` текущего Pack; из соседних Pack'ов брать только форматы/шаблоны
- **Выход:** Pack с различениями, производными только от своей области знаний

## Обоснование

Нарушение правила (взятие содержания из соседних Pack'ов) ведёт к нарушению OwnerIntegrity: один факт — одно место. Соседний Pack становится source-of-truth за чужой домен, засоряется содержанием, для которого не является owner.

## Проверка (UB-4)

Правило закреплено как UB-4 (content copying ban) в SPF/spec/process/process-lint.md. Нарушение обнаруживаемо статически: grep соседних Pack-prefixes в целевом Pack.

## Источник

WP-474 Ф5/Ф6 dry-run: субагент брал соседние Pack'и как источник содержания для различений — обнаружен на пир-сессии 2026-07-10-18. Синхронно с FMT-exocortex-template commit c8fcea1 (pack-creator Step 2).
