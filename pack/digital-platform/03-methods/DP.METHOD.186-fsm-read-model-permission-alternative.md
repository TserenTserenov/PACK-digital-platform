---
id: DP.METHOD.186
name: "FSM Read-Model как альтернатива permission-системе при N состояниях на пользователя"
type: method
pack: PACK-digital-platform
domain: digital-platform / authorization
kind: Method
status: active
created: 2026-07-14
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-07
sources:
  - "WP-467 ревью (Андрей), прецедент WP-457 ArchGate 08.07, commit 30421a1c5; session-close 2026-07-10"
related:
  complements: [DP.D.034]
  see_also: []
tags: [authorization, fsm, read-model, event-sourcing, permission-alternative, multi-state]
schema_version: 1
---

# DP.METHOD.186 — FSM Read-Model как альтернатива permission-системе (N состояний на пользователя)

## Определение

Паттерн авторизации для задачи «N типов состояния на одного пользователя, одна точка чтения»: вместо permission-библиотеки (Keto/Casbin/OPA) — event-sourced read-model таблица, куда FSM-владелец пишет текущее состояние по каждой оси.

## Архитектура

```
FSM-владелец → эмитит event → adapter → read-model (public.user_axis_state) → потребители читают напрямую
```

Потребители делают `SELECT axis_value FROM user_axis_state WHERE user_id=? AND axis=?` — нет нового сервиса, нет permission-evaluation overhead.

## IPO

- **Вход:** пользователь + набор осей (mastery, belonging, engagement...) + события переходов FSM
- **Процесс:** FSM при переходе → эмитит event → adapter обновляет строку в read-model
- **Выход:** read-model таблица, актуальная на момент последнего события; потребители читают напрямую

## Когда применять

- Число типов состояния = N (не просто «есть/нет»)
- Состояние меняется редко (через события), читается часто
- Нет требования к fine-grained permission evaluation в реальном времени
- Уже есть FSM с event-sourcing в той же системе

## Когда НЕ применять

- Нужна матрица «пользователь × ресурс × действие» (настоящий permission graph) → тогда Keto/OPA
- Права меняются часто без FSM-событий → read-model устаревает быстрее, чем обновляется
- Product-level entitlements с N продуктами → текущая схема «одно значение на ось» потребует расширения

## Прецедент (WP-457, задеплоен в прод 07.07.2026)

Три из шести осей платформы (mastery, belonging T0→T1, engagement) уже живут по этому паттерну. ArchGate пройден 08.07. Остальные оси — кандидаты при появлении FSM.

## Правило применения

**Перед предложением новой permission-библиотеки:** проверить, есть ли в той же системе задеплоенный FSM read-model паттерн, решающий ту же задачу. Если есть и прошёл ArchGate — предпочесть его как первый вариант.

## Связи

- [DP.FM.292] JWT boolean-флаг ломает мульти-продукт — failure mode, от которого защищает этот метод (FSM read-model вместо флага)
