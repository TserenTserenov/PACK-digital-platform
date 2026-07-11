---
id: DP.M.366
name: "Detector Baseline Forward Only"
name_ru: "Baseline детектора только вперёд от первого tagged-события"
name_en: "Detector baseline starts from the first tagged event, not from script creation date"
summary: "Ретроспективное сканирование без ground truth даёт ложную уверенность. Baseline нового детектора ставится с даты первого события с явными метками, не с даты создания скрипта."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: observability
valid_from: 2026-07-02
related:
  see_also: [DP.M.331]
tags: [detector, baseline, observability, ground-truth, monitoring, retroactive-scan]
source: "WP-450 Ф1, session-transcript 2026-07-02-09, commit 7a1a65a (extensions/week-close.after.md)"
schema_version: 1
---

# DP.M.366 — Baseline детектора только вперёд от первого tagged-события

## Описание

При запуске нового детектора на существующей базе логов ретроспективное сканирование без ground truth не даёт достоверного тренда.

Pattern-matched канал (regex, эвристика) калибруется по tagged-каналу (явные метки). Если в истории нет явных меток — нет возможности определить recall/precision regex → ретроспективный счётчик бесполезен для тренда.

## Algorithm

### Step 1: Определить дату первого tagged-события

Найти самую раннюю запись с явным тегом (hand-labelled event, explicit `fault_subtype=X`) в исторических данных. Если таких записей нет — baseline начинается с момента первого live-события после запуска детектора.

### Step 2: Установить baseline с этой даты

```
baseline_start = min(timestamp of tagged events)
                 OR deployment_date  # если нет исторических тегов
```

Не брать дату создания скрипта или начало имеющихся логов как baseline.

### Step 3: Считать метрики только после baseline

Метрики детектора (recall, precision, тренд срабатываний) рассчитываются только на периоде `[baseline_start, now]`. Данные до baseline_start можно исследовать вручную, но не включать в автоматический тренд.

## When to use

- При запуске нового детектора поверх существующих логов без ретроспективной разметки
- При добавлении нового тега/категории в систему мониторинга
- При оценке качества pattern-matched детектора (regex, heuristics)

## Тест применимости

«Есть ли в исторических данных явные метки для этого детектора?»
- Да → baseline = дата первого tagged-события
- Нет → baseline = дата первого live-события после запуска → retroactive scan не проводить

## Аналог

ML-модель без тестового набора = нет метрики качества. Ретроспективное сканирование без ground truth — тот же паттерн на уровне мониторинга.

## Связи

- DP.M.331 (Agent Audit Trail) — контекст: новые поля в audit trail также требуют forward-only baseline
