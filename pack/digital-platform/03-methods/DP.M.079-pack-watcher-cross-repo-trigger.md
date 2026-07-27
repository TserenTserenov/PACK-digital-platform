---
id: DP.M.079
name: Pack-watcher cross-repo trigger
name_ru: Pack-watcher — кросс-репо триггер синхронизации
name_en: Pack-Watcher Cross-Repo Trigger
type: method
status: emerging
summary: "Push-trigger из Pack-репо (SoT) в downstream-репо через GitHub Actions repository_dispatch. Заменяет polling-cron на push-on-change. Применим к Pack→curriculum, Pack→personal-guide regen, Pack→reward_rules sync."
created: 2026-05-17
trust:
  F: 2
  G: domain
  R: 0.75
epistemic_stage: emerging
related:
  uses: [DP.M.041]
  references: [DP.M.011]
  realized_by: [notify-curriculum.yml]
tags: [pack, sot, downstream, sync, webhook, ci]
wp: WP-322
sources:
  - PACK-personal commit dfdd8c0 (notify-curriculum.yml)
  - PD.FORM.103
---

# DP.M.079 — Pack-watcher cross-repo trigger

## Контекст

Pack как source-of-truth эволюционирует независимо от downstream-производных (curriculum-репо, готовые материалы, переводы, конфиги). Без явного механизма синхронизации downstream устаревает тихо — drift через 2-3 месяца обнаруживается случайно.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость синхронизации (push-trigger, zero-latency через `repository_dispatch`) ↔ стоимость поддержки инфраструктуры | Метод меняет «тихий drift раз в 2-3 месяца» на постоянную зависимость — PAT в Actions secrets, matching workflow сразу в двух репо — которую нужно держать рабочей, а не настроить один раз и забыть |
| Явная зависимость Pack→downstream в коде (аудит-трейл виден по `paths:`-фильтру) ↔ единая точка отказа самого механизма | Если `CURRICULUM_DISPATCH_TOKEN` истечёт или workflow сломается в downstream-репо, drift снова становится тихим — просто отложенным до момента, пока кто-то не заметит, что триггер сам перестал работать |

## Метод

На push в SoT-файл Pack'а:

1. GitHub Actions workflow в Pack-репо триггерится по `paths:` фильтру (изменения в `pack/<domain>/<file>`).
2. Workflow вызывает `repository_dispatch` с custom event-type (например, `pack-drift-check`) в downstream-репо.
3. Downstream-репо имеет matching workflow с `on: repository_dispatch: types: [pack-drift-check]`, который запускается → проводит drift-check / regeneration / notify.

**Требует:** PAT с правами `repo` в Pack-репо Actions secrets (например, `CURRICULUM_DISPATCH_TOKEN`).

## Тест применимости

«Есть ли производные репо, которые ДОЛЖНЫ ре-сгенерироваться или валидироваться при изменении Pack-файлов?»

- Да → Pack-watcher вместо cron в downstream.
- Нет → метод не нужен.

## Преимущества vs альтернативы

| Альтернатива | Минус |
|--------------|-------|
| Cron-polling в downstream | Latency = период; downstream должен знать Pack-расписание |
| Ручная синхронизация | Drift через 2-3 месяца; нет SLA |
| Отсутствие синхронизации | Тихий drift, обнаруживается случайно |
| **Pack-watcher** | Требует PAT, но zero-latency + явная зависимость в коде |

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Epistemic stage карточки — `emerging`: пометка `tentative` по прецеденту SA.METHOD.001 (WP-448 Ф12)._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Настройка триггера перетягивает внимание от приёмника | Внимание съезжает на шаги 1-2 в Pack-репо (paths-фильтр + `repository_dispatch`) и недооценивает шаг 3 — matching workflow в downstream-репо, который тоже должен существовать и корректно реагировать на `types: [pack-drift-check]`; без него событие уходит в пустоту, а Pack-репо выглядит «настроенным» |
| _(tentative)_ Настройка PAT воспринимается как разовая задача | Внимание тянется к первоначальному созданию токена и секрета, но не доходит до вопроса его истечения/ротации — в отличие от cron-polling, где деградация видна по расписанию, здесь молчаливый отказ триггера из-за протухшего PAT неотличим от «Pack давно не менялся» |

## Применимость

- Pack-watcher → curriculum-репо (PD.FORM.* changes)
- Pack → personal-guide regeneration (WP-322)
- Pack → reward_rules sync (см. feedback_dual_run_event_catalog_gaps.md)

## Антипаттерн

Cron-polling в downstream без знания о моменте Pack-изменения. Латентность = период polling'а; нет аудит-трейла, который файл изменился.

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
