---
id: DP.M.370
name: "Dual-source behavioral scanner"
name_ru: "Двухканальный детектор поведенческих паттернов"
name_en: "Dual-source behavioral scanner: tagged (high-confidence) + pattern-matched (low-confidence)"
summary: "Детектор поведенческого паттерна в логах с двумя независимыми каналами: tagged (явная метка, confidence=high) + pattern-matched (regex, confidence=low). Каналы репортируются отдельно, не суммируются. Baseline — с даты первого tagged-события, не с даты создания скрипта."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: observability
valid_from: 2026-07-02
related:
  see_also: [DP.M.369]
tags: [detector, behavioral, dual-source, tagged, pattern-matched, observability, audit-log, baseline]
source: "session-close 2026-07-02, WP-450 Ф2 (commit d91c424, scripts/verify-distinctions-compression.py)"
schema_version: 1
---

# DP.M.370 — Двухканальный детектор поведенческих паттернов

## Описание

Детектор поведенческого паттерна в логах работает по двум независимым каналам:

1. **Tagged-канал** (confidence=high): явный `fault_subtype='<pattern>'` в записи — ground truth, высокая точность.
2. **Pattern-matched канал** (confidence=low): regex по содержимому без явного тега — вспомогательный, высокая полнота.

Каналы **не суммируются** — репортируются отдельно с разными уровнями доверия.

## Algorithm

### Step 1: Запрос к каналам независимо
