---
id: DP.D.326
type: distinction
status: active
created: "2026-09-17"
valid_from: "2026-09-17"
name: "SET search_path на выделенном session-соединении ≠ SET search_path на pooled transaction-mode соединении"
name_ru: "SET search_path на выделенном session-соединении ≠ SET search_path на pooled transaction-mode соединении"
name_en: "SET search_path on a dedicated session connection ≠ SET search_path on a pooled transaction-mode connection"
summary: "На выделенном session-соединении search_path, установленный один раз, действует на все последующие запросы сессии. На pooled transaction-mode соединении (Neon, PgBouncer transaction pooling) каждый запрос логической сессии может обслуживаться другим физическим соединением, поэтому полагаться на search_path нельзя — безопасна только явная квалификация schema.table в каждом запросе."
pack: PACK-digital-platform
domain: digital-platform / postgresql
schema_version: 1
trust: confirmed
epistemic_stage: observed
source: "git commit eef3c4cf в aist_bot_newarchitecture, db/queries/mentorship.py; WP-253"
extraction_report: "DS-my-strategy/inbox/extraction-reports/2026-09-20-inbox-check.md"
source_candidate: 5
related:
  see_also: [DP.FM.442, DP.M.258]
---

# DP.D.326 — SET search_path на session-соединении ≠ на pooled transaction-mode соединении

## Различение

| | Выделенное/session-соединение | Pooled transaction-mode соединение (Neon, PgBouncer transaction pooling) |
|---|---|---|
| **Что происходит с `search_path`** | Установленный в рамках сессии `search_path` сохраняется для всех последующих запросов ЭТОЙ сессии | Каждый запрос логической сессии может обслуживаться РАЗНЫМ физическим соединением пула — ранее установленный `search_path` не гарантирован для следующего запроса |
| **Неквалифицированное имя таблицы (`tariffs`)** | Резолвится предсказуемо | Резолвится непредсказуемо — зависит от того, какое физическое соединение попало в пул именно сейчас |
| **Что безопасно** | Безопасно полагаться на `search_path`, однажды установленный для сессии | Безопасна ТОЛЬКО полная квалификация `schema.table` в каждом запросе — полагаться на `search_path` нельзя никогда |

## Механизм

В `aist_bot_newarchitecture` правило «всегда квалифицируй имя таблицы схемой» уже соблюдалось в SQL-миграциях (SECURITY DEFINER функции), но было пропущено в прикладном Python-слое (`db/queries/mentorship.py`). Пропуск поймал регрессионный тест в CI, а не прод: до какого-либо инцидента.

## Тест различения

«Этот SQL выполняется на pooled/transaction-mode соединении (Neon, PgBouncer transaction pooling)? Есть ли в запросе хоть одно неквалифицированное имя таблицы?» Да на оба → риск: полагаться на `search_path` этого соединения нельзя, обязательна явная квалификация `schema.table`.

## Границы

Применимо к SQL, выполняемому на pooled/transaction-mode соединении, где в запросе есть хотя бы одно неквалифицированное имя таблицы. На выделенном session-соединении `search_path`, установленный для сессии, сохраняется на все её последующие запросы, и полагаться на него безопасно.

## Происхождение и статус проверки

Источник: git commit `eef3c4cf` в `aist_bot_newarchitecture`, `db/queries/mentorship.py`. Связь: WP-253 (mentorship), CI L1+L2 smoke.

Смежно, не дубль:
- `DP.FM.442` (session-level search_path в закешированном пуле соединений маскирует существующие таблицы под «relation does not exist») — companion. DP.FM.442 описывает СЛЕДСТВИЕ для наблюдателя/диагностики: чужой/устаревший `search_path` застрял в конкретном закешированном соединении и вводит диагностику в заблуждение. Это различение — ПРИЧИНУ на уровне контракта кода: почему нельзя полагаться на `search_path` в pooled-режиме вообще, независимо от того, застрял ли там посторонний override. Общий корень: `search_path` нестабилен/непредсказуем на pooled-соединении.
- `DP.M.258` (разрешение имён внутри тела триггер-функции) — другой механизм: разрешение имён ВНУТРИ функции при её выполнении на сервере, а не между последовательными запросами клиента.

Описанная проверка задаёт критерий приёмки. Её прохождение исходной реализацией этой карточкой не утверждается.
