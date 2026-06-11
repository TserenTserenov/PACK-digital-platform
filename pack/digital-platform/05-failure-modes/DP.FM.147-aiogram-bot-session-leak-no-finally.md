---
id: DP.FM.147
name: "aiogram Bot() без try/finally session.close() → leak HTTP-коннектов в scheduler"
name_ru: "Утечка сессии бота aiogram без try/finally в планировщике"
name_en: "aiogram Bot session leak without try/finally in scheduler"
summary: "Bot() создаётся per-call в scheduler, session.close() стоит после падающих операций без try/finally — при исключении HTTP-соединение к Telegram остаётся открытым, дескрипторы растут."
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: resource-lifecycle
severity: medium
valid_from: 2026-06-09
related:
  see_also: []
tags: [aiogram, bot-session, resource-leak, scheduler, apscheduler, asyncio, try-finally, http-connection, file-descriptors]
source: "session 2026-06-09, инцидент aist-bot scheduler + memory/lessons_aiogram_bot_session_leak.md"
schema_version: 1
---

# DP.FM.147 — aiogram Bot session leak в scheduler

## Описание

Функция создаёт `bot = Bot(token=...)` локально (не глобальный singleton), вызывается периодически из scheduler (APScheduler, asyncio loop). Между `Bot(token=...)` и `await bot.session.close()` бросается исключение → HTTP-соединение к Telegram API остаётся открытым.

## Контекст возникновения

- Scheduler с частотой ≥1 раз/мин
- Bot создан per-call, не singleton
- `session.close()` вызывается ПОСЛЕ потенциально падающих операций (`get_me()`, `gather`)
- Нет `try/finally`

## Симптом

- aiohttp-коннекции не закрываются
- file descriptors растут со временем
- В долгосрочной перспективе — `OSError: too many open files`

## Корректный паттерн

```python
async def scheduled_check():
    bot = Bot(token=TOKEN)
    try:
        await bot.get_me()
        await asyncio.gather(...)
    finally:
        await bot.session.close()
```

## Детектор

```bash
grep -B1 -A20 "Bot(token" <file> | grep -c "finally:"
```

Результат 0 при наличии `Bot(token=...)` → искать явный `try/finally` в окрестности.

## Применимость

Любые боты, создающие Bot-инстанс per-call (не глобальный singleton): Telegram (aiogram), Discord, Slack. Особенно критично в scheduler-функциях.

## Отличие от соседей

Другие aiogram-captures (lines 4266 / 6049 / 6091 / 6982 в captures.md — routing / FSM / SkipHandler) описывают handler-routing аспекты. Этот FM ортогонален — про lifecycle ресурсов.
