---
id: DP.FM.167
name: "Тихий False от upstream отключает except-fallback"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-19
source: "session WP-7 TIR3 (2026-06-18-wp7-tir3-bot-tier-contract.md)"
related:
  references: [DP.FM.038, DP.FM.151]
tags: [subscription, fallback, silent-return, exception-handling, upstream]
---

# DP.FM.167 — Тихий False от upstream отключает except-fallback

## Паттерн

Upstream-функция возвращает `False` при ошибке вместо исключения. Fallback-логика написана в `except`-блоке → никогда не срабатывает. Система принимает тихий `False` за «нет данных» и отдаёт дефолтный ответ.

```python
# Антипаттерн: fallback в except-блоке
try:
    has = await upstream.has_subscription(user_id)
    if has:
        return True
except Exception:
    return await fallback_source.check(user_id)  # никогда не вызывается

# Правильно: fallback при False тоже
has = await upstream.has_subscription(user_id)
if has:
    return True
return await fallback_source.check(user_id)  # равноправный источник
```

## Следствие

Пользователи, данные которых есть только в fallback-источнике (e.g. legacy `subscription.contract` таблица из переноса LMS), всегда получают дефолтный tier. Симптом: продакшн работает, ошибок нет, но сегмент пользователей видит неверный тир.

## Диагностика

1. «Что возвращает upstream при ошибке — exception или default value (False/None)?»
2. Если default → fallback на `except` слеп к ошибкам upstream.
3. Проверить: вызвать upstream с несуществующим ID → exception или False?

## Тест обнаружения

- Нет исключений в логах, но часть пользователей с валидными данными в fallback-источнике получает дефолт → этот FM.
- Добавить `assert fallback_source.check.called` в тест, имитирующий отказ upstream → если ложь → FM подтверждён.

## Профилактика

Для каждого multi-source gate: явно определить «что является сигналом к fallback» — exception, False, None, HTTP 4xx — и написать условие явно.

## Связи

- **DP.FM.038** — Validator silent-pass: оба создают false-green через «нет исключения → всё ОК»
- **DP.FM.151** — Subscription gate multi-path divergence: тот же домен, другой механизм

## Контекст

539 T3/T4-подписчиков видели T1-тир в боте. Два источника подписки: Aisystant (новая система) + legacy `subscription.contract` (479 активных записей). Fallback был на `except` → `has_active_subscription` возвращал `False` без exception → `contract` никогда не проверялся. Дополнительный баг: `contract` искался по `aisystant_id` (int), а `contract.account_id = Ory UUID` → каст падал в `except` молча → правильный фикс: join по `ory_id` через `public.users`. Источник: WP-7 TIR3, 2026-06-18.
