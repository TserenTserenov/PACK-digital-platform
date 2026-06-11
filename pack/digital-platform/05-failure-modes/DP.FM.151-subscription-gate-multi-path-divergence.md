---
id: DP.FM.151
name: Subscription gate multi-path divergence
name_ru: "Расхождение проверки подписки по разным путям токена"
name_en: "Subscription gate multi-path divergence"
summary: "В OAuth с двумя типами токенов (JWT и opaque) проверка подписки дублируется в нескольких путях кода — фикс одного пути не покрывает другой, один тип клиента проходит, другой блокируется при том же тарифе."
type: fm
domain: digital-platform
pack: PACK-digital-platform
trust: confirmed
epistemic_stage: 2
valid_from: 2026-06-10
related:
  see_also: [DP.FM.148, DP.FM.149]
source: "session-close 2026-06-10, WP-392 hermes_chat fix"
---

# DP.FM.151 — Subscription gate multi-path divergence

## Описание

В OAuth-системе с несколькими типами токенов (JWT и opaque) subscription/permission gate дублируется в нескольких path кода. Правки в одном path не покрывают другой — один тип клиента проходит, другой блокируется при том же tier пользователя.

## Механизм возникновения

1. Ory Hydra authorization_code flow выдаёт opaque токен (не JWT) по умолчанию.
2. Сервер обрабатывает два варианта: Try0 (JWT-верификация локально) + Try1 (userinfo endpoint).
3. Subscription gate написан дважды — в Try0 и Try1.
4. Разработчик фиксит формулу в Try0 (`tier === "T2"` → `tier in ["T2","T3","T4"]`).
5. Try1 остаётся со старой формулой → клиенты с opaque токеном по-прежнему блокируются.

## Диагностика

- Один клиент (JWT, claude.ai) работает; другой (opaque, Hermes) — нет при том же tier.
- Unit-test проверял T2 включён, не покрывал матрицу tier × auth-path.

## Решение

Выносить проверку подписки в один хелпер `hasSubscription(tier)` — единая реализация, покрываемая матричным тестом `tier × auth-path`.

## Тест

«Формула subscription check повторяется в N местах кода?» Да → shared util + matrix test.

## Связи

- DP.FM.148 (regex-detector-semantic-blindspot) — связь метафорическая (точечный фикс не покрывает все пути), но ось другая: 148 = ограниченность инструмента, 151 = divergence кода + test gap. Не дубль.
- DP.FM.149 (channel-style-bleed) — смежный паттерн: код разветвляется, а логика должна быть единой.
