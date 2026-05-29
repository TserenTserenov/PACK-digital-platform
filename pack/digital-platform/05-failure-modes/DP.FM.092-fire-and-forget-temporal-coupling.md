---
id: DP.FM.092
name: "Fire-and-forget temporal coupling со streak/бизнес-логикой"
type: fm
domain: digital-platform
status: active
trust: confirmed
epistemic_stage: 3
valid_from: 2026-05-28
sources:
  - "session-transcript 2026-05-28 (peer-сессия bot-fixes-review)"
---

# DP.FM.092: Fire-and-forget Temporal Coupling

## Описание

Middleware выполняет write fire-and-forget в фоне. Последующий синхронный read в том же handler видит уже обновлённое состояние → бизнес-логика срабатывает некорректно.

## Симптомы

- Middleware пишет `last_active_date = today` (fire-and-forget)
- Handler тут же читает `last_active == today` → ранний return
- Streak / бизнес-счётчик не инкрементируется, хотя внешне всё выглядит нормально

## Корневая причина

Одно поле (`last_active_date`) используется двумя независимыми контрактами:
- DAU-учёт (middleware обновляет при каждом событии)
- Streak-trigger (handler проверяет «был ли сегодня уже учтён»)

Когда write middleware опережает read handler (даже при «пожарном» запуске), семантический конфликт активируется.

## Последствия

- Streak не обновляется несмотря на активность пользователя
- Ошибка silent — нет ни exception, ни warning
- Тест на unit-уровне не поймает: race condition проявляется только при конкретной последовательности async-операций

## Решение

**Вариант A (рекомендуемый):** раздельные поля по контракту:
- `last_seen_date` — для DAU-учёта (пишет middleware)
- `last_streak_date` — для streak-trigger (пишет streak-логика)

**Вариант B:** SELECT FOR UPDATE в критической секции streak-логики — гарантирует чтение состояния до write middleware.

## Детектор

«Одно DB-поле используется middleware И бизнес-логикой с разными семантическими контрактами?»
Да → fire-and-forget temporal coupling возможен.

## Применимость

Любой async handler с middleware write + бизнес-логикой в том же flow.
