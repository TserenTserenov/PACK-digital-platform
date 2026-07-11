---
id: DP.M.368
name: "Per-file byte/token ratio calibration"
name_ru: "Пофайловая калибровка соотношения байт/токен"
name_en: "Per-file byte/token ratio calibration via per-file median"
summary: "MEDIAN_RATIO = медиана (bytes(f)/tokens(f)) по всем файлам выборки — не одиночный вызов токенайзера на конкатенации. Граничные эффекты BPE при склейке дают смещённое effective ratio, а не константу пофайловой нормализации."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: context-budgeting
valid_from: 2026-07-02
related:
  see_also: [DP.D.238, DP.FM.248]
tags: [context-budgeting, tokenization, BPE, byte-token, calibration, per-file]
source: "session-close 2026-07-02 (WP-450 Ф3, возражение Kimi)"
schema_version: 1
---

# DP.M.368 — Пофайловая калибровка соотношения байт/токен

## Описание

Для бюджетирования контекста требуется константа MEDIAN_RATIO = медиана соотношения bytes/tokens. Методологически корректный способ: считать это соотношение **пофайлово**, затем брать медиану значений — не запускать токенайзер на конкатенации всех файлов.

## Algorithm

### Step 1: Собрать пофайловые соотношения
