---
id: DP.D.117
name: "Два render pipeline'а ≠ два продукта ≠ два региона"
type: distinction
domain: digital-platform
pack_refs: [DP.D.030, DP.IWE.006]
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "peer-сессия 2026-05-29-14-mim-as-it-company, Тема 8 «Production-контуры A vs B/C»"
---

# DP.D.117 Render pipelines ≠ Products ≠ Regions

## Различение

| | Два продукта | Два региона | Два render pipeline'а |
|---|-------------|------------|----------------------|
| **Source** | Разный | Идентичный | Один |
| **Контракт** | Разный | Один | Один |
| **Команды** | Независимые | Одна | Одна |
| **Цель multi-deploy** | Разное value-proposition | Latency / HA | Разные target-формат / аудитория |
| **Governance-контур** | N независимых | Один (репликация) | Один (один source) |
| **Пример** | Aisystant + IWE | EU + US instances | iOS + Android из одного code-base, public-руководство + персональное-руководство из одного Pack+CAT |

## Тест

«Если изменить source, обновятся ли оба output одновременно?»
- Да → два pipeline'а одного source
- Нет → два продукта

«Output идентичен, отличается только размещение?»
- Да → два региона
- Нет → не региональная репликация

## Применение

При multi-target deployment (web / Telegram / PDF, public / private, universal / personalized):
- Один source → один governance-контур, N pipeline'ов разделяют его
- Compliance / observability / release-процедуры **не удваиваются** на каждый pipeline
- Изменение source → автоматическое обновление всех pipeline'ов (если pipeline'ы pull, не fork)

## Антипаттерн

Удваивать governance / compliance / observability на каждый pipeline («раз отдельный контур — отдельный security audit, отдельный release notes, отдельный change-log») → лишняя бюрократия там, где source един.

Маркировать как «региональный deployment» pipeline, который на самом деле производит **другой** артефакт из того же source → ожидание идентичности output, нарушение этого ожидания.

## Связи

- DP.D.030 — общая deployment-topology (контекст)
- DP.IWE.006 — каналы доставки персональных руководств (применение)
