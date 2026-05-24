---
id: DP.FM.074
name: "State-machine callback handler without router wire-up = silent dead-end"
type: failure-mode
trust: validated
epistemic_stage: confirmed
valid_from: 2026-05-22
domains: [bot, aiogram, fsm, ui]
related:
  see_also: [DP.FM.073]
  references: [WP-327]
---

# DP.FM.074 — Callback handler в SM-state без регистрации в router = silent UI dead-end

## Описание

При добавлении нового utility-state в State Machine бота (aiogram + FSM) разработчик пишет `handle_callback` внутри файла стейта (`states/utilities/*.py`) и считает работу законченной. UI-кнопки рендерятся, но клик ничего не делает — событие до handler'а не доходит.

## Симптом

- Кнопка появляется в чате, клик регистрируется (BotFather логи), но handler не вызывается.
- В логах нет ошибки — просто тишина.
- Reproducible 100% для нового callback prefix; не воспроизводится для уже-зарегистрированных prefix'ов.

## Причина

aiogram-router фильтрует callback'и по **двум** условиям одновременно:

1. `_is_in_sm_<state>_state(state)` — FSM-проверка текущего стейта.
2. `F.data.startswith(<prefix>)` — фильтр по prefix'у callback_data.

Оба фильтра задаются в **router-файле** (`handlers/callbacks.py`), не в файле стейта. Handler в файле стейта существует, но router отбрасывает callback на уровне раньше — handler не вызывается.

## Класс failure

**Handler-registration gap**: вторая половина wire-up забывается, потому что handler «выглядит готовым» в файле стейта. Артефакт работает только при наличии **обеих** частей.

## Решение

При добавлении нового callback prefix в SM:

1. Создать `handle_callback` в `states/utilities/<state>.py`.
2. **Зарегистрировать** в `handlers/callbacks.py`:
   ```python
   @router.callback_query(
       lambda c, state: _is_in_sm_<state>_state(state),
       F.data.startswith("<prefix>")
   )
   async def <state>_callback(callback: CallbackQuery, state: FSMContext):
       await <state>_handler.handle_callback(callback, state)
   ```

## Тест обнаружения

После добавления нового callback prefix в SM:
```bash
grep -c "<prefix>" handlers/callbacks.py
# должно быть ≥1; если 0 — handler не подключён
```

CI/pre-commit hook: для каждого `F.data.startswith("X")` в `states/utilities/*.py` должен быть `F.data.startswith("X")` в `handlers/callbacks.py`.

## Прецеденты

1. **2026-05-22** — WP-327 Ф5b utility-stейты `/mydata`, `/points` (commit 0b5eac2e). Handler написан, router забыт → UI silent dead-end.

## Применимо

Все aiogram-боты с State Machine + utility-стейтами (`/mydata`, `/points`, `/help`, любые callback_data-driven UI). Аналог в python-telegram-bot: `Application.add_handler` без вызова = тот же класс fail.

## Связи

- DP.FM.073 (numeric type coercion at boundary) — другой класс boundary-фейлов.
- Распознавание паттерна: «handler выглядит готовым в одном месте, нужна регистрация в другом».
