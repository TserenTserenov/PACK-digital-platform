---
id: DP.FM.041
title: "Dedup Content Slice False Positive — срез строки как ключ дедупликации"
type: failure-mode
status: active
pack: PACK-digital-platform
domain: digital-platform
valid_from: 2026-05-15
tags: [dedup, hash, content-slice, false-positive, vector-db, memory]
source: "WP-316 fix(wp-316): dedup по display-тексту, не content[:80] (0aec1c91), 2026-05-15"
schema_version: 1
---

# DP.FM.041 — Dedup Content Slice False Positive

## Описание

При дедупликации текстовых объектов используется срез строки (`content[:80]`) вместо семантически уникального идентификатора (name, title, slug). Объекты с одинаковым шаблонным началом ложно скрываются как дубли.

## Симптом

Из 42 уникальных объектов (правила, feedback-записи, карточки) видны только 5-10. Остальные «дедуплицированы» из-за общего вводного шаблона («Правило...», «При...», «Не...»).

## Тест

Два семантически разных объекта с одинаковым началом → если дедуплицируются → ключ неверный.

## Правильный ключ

Использовать name-поле, title, slug или составной ключ (source_id + position), а не срез текста.

## Применимость

Любая система дедупликации текстовых объектов: векторные базы, feedback-памяти, кэши, дашборды правил.

## Связи

- **Источник:** fix(wp-316): dedup по display-тексту, не content[:80] (0aec1c91)
