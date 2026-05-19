---
id: DP.M.094
name: "Dual-signal enforcement gate для ритуального перехода"
type: method
status: active
created: 2026-05-19
valid_from: 2026-05-19
sources:
  - extensions/day-open.before.week-close-gate.md commit 11db3f70, DS-my-strategy 2026-05-19
related:
  refines: [AS.M.016]
  references: [DP.M.059]
---

# DP.M.094 — Dual-signal Enforcement Gate для ритуального перехода

## Определение

Паттерн принудительного контроля ритуального перехода (A→B) через OR-проверку двух независимых сигналов закрытия периода A. Если оба сигнала = 0 → переход B блокируется с инструкцией.

## Проблема

Ритуальный переход (Неделя→День, Месяц→Неделя) требует гарантии, что предыдущий период закрыт. Одного сигнала недостаточно: ручное закрытие (пользователь) и автономное (scheduler) используют разные артефакты — при проверке только одного возможен ложный пропуск.

## Структура паттерна

```
if signal_manual OR signal_autonomous:
    allow_next_ritual()
else:
    block("⛔ Предыдущий ритуал не закрыт. [Инструкция]")
```

**Сигнал 1 (ручной):** переменная окружения MANUAL_CLOSE=1 ИЛИ git log за период содержит коммит с ключевым словом («week-close», «day-close»).

**Сигнал 2 (автономный):** state-file маркер scheduler.sh (например, /tmp/week-close-done или аналогичный файловый флаг).

## Тест переносимости

«Ритуал может быть выполнен разными акторами (человек / автономный агент)?» Да → dual-signal OR-gate применим.

## Примеры применения

- **Week Close gate в Day Open:** `MANUAL_CLOSE=1` (пользователь закрыл вручную) OR `AUTO_CLOSE=1` (scheduler.sh закрыл автономно Sun 23:50) → Day Open разрешён
- **Day Close gate в Day Open следующего дня:** git log вчерашний с «day-close» OR state-file `/tmp/day-close-YYYY-MM-DD`

## Отличие от AS.M.016

AS.M.016 (Dual-signal Event Detector): OR-логика для **обнаружения факта** события (запись в лог/DB).
DP.M.094 (Dual-signal Enforcement Gate): OR-логика для **блокировки следующего шага** при отсутствии факта. Детектор → факт → реакция. Gate → факт → разрешение/блокировка.

## Связь с AS.M.030

AS.M.030 (Autonomous Safety-net Trigger) — дополняющий паттерн: гарантирует, что один из сигналов (автономный) всегда сработает. Gate + Safety-net = полное решение.
