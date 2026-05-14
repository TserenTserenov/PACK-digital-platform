---
id: DP.FM.028
name: Event Coverage Gap — новый модуль без аудита эмиссии событий
category: event-sourcing
severity: major
status: active
summary: "При добавлении нового workflow-модуля не проводится аудит event coverage: модуль доставляет пользовательские действия без эмиссии domain_event. Downstream системы (stage_evaluator, activity hub) видят пустой stream — активность пользователя не учитывается."
created: 2026-05-12
valid_from: 2026-05-12
related:
  see_also: [DP.FM.010, DP.FM.011]
tags: [event-sourcing, workflow, event-emission, stage-evaluator, downstream-blindness]
source: "WP-253 Блок 2 Quick Close — states/workshops/marathon/lesson.py без lesson_completed event (12 мая 2026)"
---

# [DP.FM.028] Event Coverage Gap

## Суть паттерна

При создании нового workflow-модуля (новый тип занятий, новый сценарий взаимодействия) **не проверяется, какие domain_event он должен эмитировать**. Модуль технически работает, но downstream системы (stage_evaluator, activity hub, progress tracker) его не видят — пустой event stream.

## Проявление

- stage_evaluator не засчитывает активность по новому типу занятий
- Пользователь проходит марафон/воркшоп, но его ступень не растёт
- Баг не виден в логах модуля — только в отсутствии данных в downstream

## Правило профилактики

При создании нового workflow-модуля с пользовательскими действиями — **обязательный checklist**:
1. Какие события должен эмитировать модуль? (lesson_completed, practice_done, day_closed...)
2. Эти события покрыты в domain_event schema?
3. Тест: после user action в модуле — есть ли запись в domain_event?

## Решение

Добавить `asyncio.create_task(emit_event(...))` как fire-and-forget в каждом значимом user action.

## Связи

- Расширяет DP.FM.011 (не-захват паттернов) для event-sourcing контекста
- Влияет на DP.ARCH.006 (Память = Observed события)
