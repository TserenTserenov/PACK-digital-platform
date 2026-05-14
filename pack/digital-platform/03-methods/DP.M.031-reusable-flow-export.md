---
id: DP.M.031
name: Reusable Flow Export — экспортируемая функция для множественных точек входа
type: method
status: active
valid_from: 2026-05-12
summary: "Функция UI-flow (consent, onboarding, активация) оформляется как reusable export из своего модуля, а не как inline-код в одном handler. Позволяет нескольким entry points (deep-link, команда, кнопка, QR-код, UTM-параметр) делегировать единой реализации без дублирования."
related:
  informs: [DP.ARCH.001]
tags: [bot, telegram, onboarding, consent, reusability, entry-points, deep-link]
source: "WP-188 Ф17 — show_consent_optin() как reusable export (12 мая 2026)"
---

# [DP.M.031] Reusable Flow Export

## Суть метода

Функция UI-flow (consent, onboarding, активация фичи) оформляется как **именованный экспорт** из модуля-владельца (consent.py, onboarding.py). Любые точки входа **импортируют и вызывают** эту функцию — не дублируют логику.

## Алгоритм

1. Определить: кто «владеет» flow? (consent.py для consent flow)
2. Вынести flow-функцию как экспорт модуля: `async def show_consent_optin(message, state)`
3. Все entry points импортируют: `from handlers.consent import show_consent_optin`
4. Emit-события добавлять через fire-and-forget: `asyncio.create_task(emit_event(...))` — не блокирует UI

## Применимость

Любой flow с несколькими точками входа:
- Команда `/consent` → `cmd_consent`
- Deep-link `t.me/bot?start=consent` → `start_handler`
- Кнопка в меню → `button_handler`
- Referral link, QR-код, UTM-параметр

## Тест применения

«Если я добавлю новую точку входа — сколько строк кода нужно дублировать?» → Если ноль (только вызов функции) — паттерн применён правильно.

## Связи

- Применяется в: consent flow, onboarding flow, любом opt-in flow
- Complementary: DP.FM.028 (без fire-and-forget emit → event coverage gap)
