---
id: DP.M.233
name: "Cutover-date в детекторе вместо backfill legacy state"
type: method
domain: digital-platform
pack_refs: []
trust: medium
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
last_updated: 2026-08-01
source: "WP-358 Ф10 — scripts/check-open-sessions.sh (commit 850c5c2a)"
---

# DP.M.233 Cutover-date в детекторе вместо backfill legacy state

## Описание

Метод деплоя нового детектора (open sessions, drift watcher, hygiene check) для системы с накопленным legacy state: вместо переоформления N legacy-файлов в формат, ожидаемый детектором, зашить в детектор дату активации — файлы старше даты игнорируются.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость deploy детектора ↔ полнота видимости legacy | Cutover позволяет запустить детектор сегодня, но legacy state становится невидимым для отчётов и метрик going-forward |
| Стоимость миграции формата ↔ риск потери истории | Backfill дорог и рискован, но сохраняет возможность агрегировать legacy + new; cutover дёшев, но отрезает историю |
| Гигиена going-forward ↔ compliance/бизнес-агрегация | Если legacy не влияет на бизнес-логику, cutover избавляет от шума; если legacy нужен для отчётов или compliance, отсутствие backfill даёт дефект данных |

## IPO

**Вход:**
- Новый детектор (валидатор формата, hygiene check, drift watcher).
- Корпус legacy-файлов в старом формате.

**Процесс:**
1. Применить тест: «потребует ли отчёт/решение пересчёта по legacy?»
2. Нет → cutover: зашить `<DETECTOR>_CUTOVER_DATE=YYYY-MM-DD` в код детектора; файлы старше даты пропускать.
3. Да → backfill: подготовить миграцию legacy в новый формат.

**Выход:** Детектор активен на going-forward корпусе; legacy сохранён as-is; нет шума при обращении к старому контенту.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Предпочтение быстрого cutover как «win» | Давление «закрыть задачу» преувеличивает кажущуюся дороговизну backfill и недооценивает вероятность, что legacy понадобится для агрегации, биллинга или отчётности |
| «Legacy не критичен» без проверки business decisions | Склонность принимать решение о cutover на основе интуиции, а не явного теста: «потребует ли отчёт/решение пересчёта по legacy?» |
| Забывание cutover date как technical debt | Дата активации зашивается в код и остаётся «невидимой» для новых участников команды, которые позже интерпретируют отсутствие legacy-видимости как баг |

## Когда применим cutover

- Legacy не критичен для бизнес-логики (только гигиена going-forward).
- Миграция формата дороже потери видимости в legacy.
- Ожидается естественный churn legacy за разумное время.

## Когда необходим backfill

- Legacy state влияет на business decisions (метрики, отчёты, billing).
- Есть отчёт/метрика, агрегирующая legacy + new.
- Compliance требует единообразного формата для всей истории.

## Пример: WP-358 Ф10 check-open-sessions.sh

`SESSION_CUTOVER_DATE=2026-05-29` зашит в детектор. 52 pre-Ф10 файла НЕ показываются. Решение пилота: backfill не нужен — legacy не агрегируется в отчёты.

## Применение

Migration patterns: feature flag rollout, schema versioning, новые валидаторы поверх старого корпуса; deploy hygiene-detectors.

## Связи

- Источник: WP-358 Ф10 (commit 850c5c2a)
- DP.M.234 (Открытая сессия — двухусловное определение) — родственный паттерн
