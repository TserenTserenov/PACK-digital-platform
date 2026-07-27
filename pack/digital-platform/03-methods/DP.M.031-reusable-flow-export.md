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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Централизация flow-логики в одном экспорте (шаг 2) ↔ гибкость под специфику точки входа | Единая функция `show_consent_optin` для всех entry points (deep-link, команда, кнопка, QR, referral, UTM) устраняет дублирование, но если конкретной точке входа нужно небольшое отличие в поведении, метод не даёт явного механизма параметризации — риск либо разрастания сигнатуры функции, либо возврата к дублированию «в обход» |
| Fire-and-forget emit событий (`asyncio.create_task`, шаг 4) ↔ надёжность доставки события | Неблокирующий emit не задерживает UI-ответ пользователю, но именно поэтому ошибка внутри `emit_event` не всплывает синхронно и не блокирует flow — метод явно фиксирует этот компромисс через complementary-связь с DP.FM.028 (event coverage gap) |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Похожий handler перетягивает от канонического экспорта | При добавлении новой точки входа внимание съезжает к «похожему» соседнему handler'у вместо поиска канонического экспорта модуля-владельца — особенно когда разработчик новой точки входа не знаком с consent.py/onboarding.py и быстрее находит пример «рядом», чем ищет реальный экспорт, который нужно импортировать (шаг 3) |
| Emit-вызов воспринимается как «сделано» без проверки доставки | Fire-and-forget emit (шаг 4) воспринимается как завершённый сразу после написания `asyncio.create_task(emit_event(...))`, и внимание переключается на следующую задачу — практикующий систематически недооценивает необходимость отдельно проверить, что событие реально долетает (DP.FM.028), потому что неблокирующий вызов не даёт немедленной обратной связи об ошибке |

## Связи

- Применяется в: consent flow, onboarding flow, любом opt-in flow
- Complementary: DP.FM.028 (без fire-and-forget emit → event coverage gap)

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 2). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
