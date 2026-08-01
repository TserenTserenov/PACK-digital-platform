---
id: DP.M.198
name: "Атомарный переход в degrade-state: state + user-reply одним PUT"
type: method
pack: PACK-digital-platform
domain: graceful-degradation
trust: 0.85
epistemic_stage: confirmed
valid_from: 2026-05-28
source: WP-358 Ф8 peer-session 2026-05-28-05, commit aa85f81e (fix api_unavailable system reply)
---

# DP.M.198 — Атомарный переход в degrade-state: state + user-reply одним PUT

## Описание

При переходе системы в состояние деградации (API недоступен, rate limit, circuit breaker) — обновление state-флага и вставка user-facing объяснения выполняются в одной атомарной операции, не двумя отдельными PUT.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Атомарность перехода в degrade-state ↔ простота двух раздельных вызовов | Один PUT `_write_system_reply(..., extra_meta)` сложнее в написании и требует, чтобы writer умел писать state и reply вместе — метод платит этой связанностью за закрытие окна рассогласования, где флаг уже выставлен, а объяснения пользователю ещё нет |
| Страховка guard'ом на стороне бота ↔ единственный owner degrade-логики | Метод запрещает дублирующий `if api_unavailable_until > now` в боте: тонкий клиент проще и не рассинхронизируется по порогам, но цена — dispatcher обязан читать флаг перед каждым вызовом LLM, исключений быть не может |

## Проблема двухшагового подхода

```python
# НЕПРАВИЛЬНО — два шага
await update_session(api_unavailable_until=...)  # шаг 1
await write_system_reply("API временно недоступен")  # шаг 2
# Между шагами 1 и 2: state inconsistent (флаг выставлен, reply не записан)
# При сбое на шаге 2: пользователь не получает объяснения, хотя dispatcher уже в degrade-режиме
```

**Проблема:** Окно между записью state и записью reply — состояние рассогласовано. Если сбой или retry между шагами, пользователь видит молчание, а не объяснение.

## Правильный паттерн

```python
# ПРАВИЛЬНО — один атомарный PUT
await _write_system_reply(
    text="API временно недоступен. Попробуйте через несколько минут.",
    extra_meta={"api_unavailable_until": (datetime.now() + timedelta(minutes=5)).isoformat()}
)
# _write_system_reply пишет и reply, и extra_meta за один PUT
```

## Дополнительный принцип: anti-spam через архитектуру

Bot-side guard (`if api_unavailable_until > now: return`) не нужен, если dispatcher уже читает этот флаг перед вызовом LLM. Дублирование логики в боте — anti-pattern: изменение порога в одном месте не синхронизируется с другим.

**Правило:** Degrade-логика живёт в dispatcher'е (single owner). Бот — тонкий клиент, не должен знать про API-флаги.

## Применимость

- Telegram-боты с dispatcher'ом при недоступности внешнего LLM/API.
- Любая система с graceful degradation: при переходе в degrade-state всегда упаковывать `(state_change, user_explanation)` в одну операцию.
- Webhook-системы: при rate limit → атомарно записать `retry_after` + вернуть 429 с телом.
- Event-sourcing системы: degrade-event содержит и state-change, и user-notification как поля одного события.

## Тест обнаружения anti-pattern

```bash
# Ищем два последовательных await/call с api_unavailable и write_reply
grep -n "api_unavailable_until" dispatcher.py
grep -n "write_system_reply\|write_reply" dispatcher.py
# Если они в разных строках без вызова _write_system_reply(...extra_meta) — FM активен
```

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Корректность state-флага затмевает доставку объяснения пользователю | Внимание практика сфокусировано на правильности `api_unavailable_until` (значение, ttl, формат), а user-facing reply воспринимается как сопутствующая мелочь — двухшаговая запись выглядит естественной, пока сбой между шагами не оставит пользователя в молчании при уже включённом degrade-режиме |
| Проверка ищется в коде dispatcher'а, а не на границе «бот ↔ dispatcher» | Внимание приковано к grep по `api_unavailable_until` в dispatcher.py, а вопрос «знает ли бот про API-флаги вообще» не задаётся — дублирующий guard прорастает на стороне тонкого клиента, где его никто не ищет, и пороги начинают жить раздельно |

## Связи

- WP-358 Ф8 — первый прецедент
- DP.FM.090 — смежный FM в том же WP-358 (guard-order)
- DP.M.160 — single-point degradation tracking (смежный паттерн мониторинга деградации)


---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 5). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
