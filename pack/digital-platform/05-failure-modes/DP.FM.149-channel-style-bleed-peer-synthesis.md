---
id: DP.FM.149
title: "Channel-style-bleed — технический стиль стенограмм заражает pilot-facing синтез"
name_ru: "Протекание технического стиля стенограмм в отчёт для пилота"
name_en: "Channel-style-bleed in peer-session synthesis"
summary: "Синтезатор читает технические turn-файлы и продолжает их стиль при записи отчёта для пилота — английские термины и машинные маркеры переползают из доказательного слоя в pilot-facing."
type: failure-mode
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: 3
valid_from: 2026-06-10
related:
  see_also: [DP.FM.148, DP.SC.050]
source: session WP-388 style-audit, 2026-06-10
---

# DP.FM.149 — Channel-style-bleed в peer-session synthesis

## Краткое описание

LLM-синтезатор читает turn-файлы (плотный технический стиль) и продолжает их стиль при записи report.md (режим «на пальцах» для пилота). Английские термины, маркеры (PASS, exit 0) переползают из доказательного слоя в pilot-facing.

## Симптомы

- report.md §1-§4 содержит технические термины: «false-positive», «orphaned», «payload_meta», «frontmatter».
- Маркеры успеха («PASS», «exit 0») появляются в синтезе для пилота.
- Пилот не может читать итоги без технического контекста.

## Контекст

Два режима письма в одной сессии: turn-файлы (стенограмма ходов) — плотный технический (правильно, доказательство); report.md §1-§4 — режим «на пальцах» (для пилота). LLM читает turn-файлы и продолжает их тон вместо переключения.

## Диагностика

Тест: «содержит ли report.md §1-§4 английские термины без перевода или маркеры типа PASS/exit?» — да → channel-style-bleed.

## Решение

Явный style-switch step в финализаторе peer-сессий: self-check из 4 пунктов перед записью report.md (детектор канала из CLAUDE.md §9 S-37). Принцип: явный переключатель, не «само получится».

## Связи

- DP.FM.148 (regex semantic blindspot) — другая ось: 148 = ограниченность детектора, 149 = генератор не переключает канал. Не дубль.
- Service Clause: DP.SC.050 (разговорный стиль) — источник правил S-37.
