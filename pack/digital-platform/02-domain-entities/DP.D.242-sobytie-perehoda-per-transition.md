---
id: DP.D.242
name: "Событие перехода (per-transition) ≠ Событие состояния (per-state)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-07-12
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.242: Событие перехода (per-transition) ≠ Событие состояния (per-state)

| Per-transition event | Per-state event |
|---------------------|-----------------|
| Эмитируется при изменении (old → new) | Эмитируется при каждом наблюдении/проверке |
| Есть только при реальном изменении | Есть всегда, даже если состояние не изменилось |
| Event log: только реальные переходы | Event log: дубли при повторных read/observe |
| Replay корректно считает переходы | Replay завышает количество «переходов» |

**Почему важно:** в event sourcing / CQRS системах с состояниями пользователя — эмиссия per-state засоряет event log дублями и делает replay некорректным. Read-model хранит текущее состояние (fast-read), event log содержит только реальные переходы.

**Тест:** «Есть ли observable change в системе?» Да → event; нет (проверка без изменения) → тишина.

**Источник:** WP-457 Ф10 belonging/engagement, session-close 2026-07-07. `user_axis_transitioned` генерируется при ИЗМЕНЕНИИ оси, не при каждом read `user_axis_state`.

**Смежно:** [DP.D.002] (Модель ≠ Данные), [DP.FM.028] (event-coverage-gap).
