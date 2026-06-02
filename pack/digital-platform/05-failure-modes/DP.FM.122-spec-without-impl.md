---
id: DP.FM.122
name: "Spec без impl — спецификация ушла вперёд кода"
type: failure-mode
domain: digital-platform
pack_refs: []
status: active
valid_from: 2026-05-30
schema_version: 1
source: "peer-session 2026-05-30-28-tg-session-hang-diagnosis (00-writer.md, гипотеза A)"
---

# DP.FM.109 — Spec без impl (спецификация ушла вперёд кода)

## Описание

Service Clause / spec обновляется с новой версией поведения (light/heavy split, прогресс-нотификации каждые 15с, turn hard cut-off 15 мин), но имплементация остаётся на старой версии. При runtime-инциденте писатель сначала ищет баг в коде, не подозревая, что код **просто не имеет** функционала из spec.

## Симптом

- «Согласно DP.SC.NNN должно было сработать X — но X не нашёл в grep».
- Silent timeouts, отсутствие ожидаемых progress signals.
- Fallback к старой logic в edge case'ах, описанных в новой версии spec.

## Тест

«Если завтра пилот будет на edge case Y из спеки — сработает ли код?» Если ответ требует чтения кода, а не спеки — spec-impl alignment не верифицирован.

## Защита

1. При апдейте спеки — обязательная импл-задача с тем же deadline.
2. Acceptance criteria для phase-close включает `grep`-кода на ключевые маркеры из spec.
3. РП-аудит «spec ↔ impl coverage matrix» — каждый MUST/SHALL из spec покрыт runtime-кодом или явно помечен `not_implemented`.

## Применимо к

- Любой evolving Service Clause / DP.SC.*
- Contract testing
- Post-incident review с расхождением «spec говорит X, runtime сделал Y»