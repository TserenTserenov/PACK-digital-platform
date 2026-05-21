---
id: DP.M.117
title: Контент cohort-программы как декларативный JSON
type: method
domain: pack/digital-platform
trust: medium
epistemic_stage: candidate
status: active
valid_from: 2026-05-20
source: captures.md L3157 (feed:session-close 2026-05-20, commit 949a5ea DS-marathon-v2-tseren)
---

# DP.M.117: Контент cohort-программы как декларативный JSON

> Cohort-программы с известной длительностью (марафон, drip-курс, onboarding серия) хранят контент в декларативном JSON `{day_N: {payload_type: …}}` **отдельно от runtime-логики**. Coupling код↔контент = антипаттерн: каждое изменение расписания = PR в код.

## Контекст

Cohort = группа участников, проходящих программу синхронно (start_date) или каскадно (rolling cohort) с фиксированным числом дней. Каждый день → 1+ единиц контента (урок, практика, чек-ин, напоминание).

## Inputs / Process / Outputs

**Inputs:**
- `content.json` файл в репо контента (отдельно от кода бота/cron):
  ```json
  {
    "version": "1.0",
    "days": {
      "1": {
        "lesson": "<markdown text>",
        "practice": "<task description>",
        "checkin": "<question>"
      },
      "2": { ... },
      "...": "..."
    }
  }
  ```
- runtime-скрипт читает `content.json`, выбирает `days[current_day]`, рассылает по типам payload.

**Process:**
1. Автор пишет / редактирует JSON.
2. CI-валидатор по схеме проверяет: все дни (1..N) заполнены, все обязательные `payload_type` есть, текст не пустой.
3. Deploy = коммит JSON (runtime читает свежую версию).
4. Runtime selects content by `current_day = (today - cohort.start_date).days + 1`.

**Outputs:** доставленные единицы контента по plan.

## Преимущества

- Автор редактирует JSON без касания кода
- Контент-валидатор проверяет полноту по схеме
- Replay/перезапуск = обновление JSON, deploy кода не нужен
- Версионирование контента через git
- Контент может быть локализован / клонирован для разных когорт

## Когда не применимо

- Динамический контент per-user (LLM-генерация, recommendations) → нужен runtime-движок, не декларативный JSON
- Длинные программы (1+ год) с непрерывным контент-выпуском → headless CMS лучше
- Контент с тяжёлыми ассетами (видео, изображения) → CDN + JSON-индекс

## Тест применимости

«Длительность программы фиксирована, число единиц контента известно заранее, контент не зависит от индивидуального состояния пользователя?» → Да → декларативный JSON.

## Связи

- **Source:** WP-330 commit 949a5ea (marathon-content.json — 14 дней × {lesson, practice, checkin})
- **Применимо к:** WP-330 (марафон), WP-149 (программа ЛР), WP-346 (R-cohort onboarding), любым drip-кампаниям, ретеншн-сериям
- **Отличается от:** headless CMS (динамика per-item, авторская workflow), runtime LLM-генерация
