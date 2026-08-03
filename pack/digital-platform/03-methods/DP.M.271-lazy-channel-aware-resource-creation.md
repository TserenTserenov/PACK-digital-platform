---
id: DP.M.271
title: "Lazy channel-aware resource creation in multi-channel onboarding"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-03
source: peer-session 2026-06-03-10, WP-7 Block MRG / WP-396, git diff DS-IT-systems/iwe-server 0b6b96a
last_updated: 2026-08-01
---

# DP.M.271 — Lazy channel-aware resource creation in multi-channel onboarding

## Описание

В multi-channel системах (бот + web) ресурсы создаются lazily в том канале, который их требует — не eager при первом onboarding-событии в любом канале.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Eager creation ↔ lazy creation | Eager упрощает код (один entry point), но создаёт неиспользуемые ресурсы; lazy сложнее, но не создаёт мусор для bot-only/web-only пользователей |
| Единый onboarding flow ↔ channel-specific | Единый flow проще для пилота и тестирования, но может создавать ресурсы в ненужном канале; channel-specific требует разделения и дополнительных проверок |
| Code simplicity ↔ resource efficiency | Легче создать всё сразу, но тратится storage и усложняется cleanup; lazy требует проверки состояния и условного создания |

## Принцип

**Eager (анти-паттерн):** ресурс создаётся при onboarding-событии в канале A, даже если пользователь никогда не откроет канал B, для которого ресурс нужен.

**Lazy channel-aware (правильно):** ресурс создаётся при первом обращении к каналу B.

## Пример

| Событие | Eager (неверно) | Lazy (верно) |
|---------|-----------------|--------------|
| Бот получил msg_7 | create_managed_repo() в фоне | ничего |
| Пользователь открыл web | — | create_managed_repo() |
| Пользователь только в боте | repo создан, но никогда не открыт | repo не создан |

## Применение

При проектировании onboarding flow в multi-channel системах:

1. Идентифицировать все ресурсы с привязкой к каналу
2. Для каждого ресурса: в каком канале он первично нужен?
3. Создание → триггер первого обращения к этому каналу, не к другому
4. Bot-only пользователь не должен получать web-ресурсы и наоборот

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Внимание уходит в единый onboarding flow, а не в channel-specific триггеры | Eager creation кажется проще, потому что один entry point легче тестировать; channel-specific lazy guard воспринимается как лишнее усложнение, пока не появляются неиспользуемые ресурсы и мусор в хранилище |
| Предпочтение видимой простоты кода вместо условного создания | Легче написать `create_all()` в onboarding handler, чем проверять состояние канала; lazy path откладывается, и bot-only/web-only пользователи получают лишние объекты |
| Метрики по основному каналу заменяют оценку cross-channel coverage | Анализируют ботовый трафик и забывают проверить, что web-only пользователь не создаёт bot-ресурсы, а bot-only не получает web-ресурсы; коллизии обнаруживаются только при cleanup |

## Связи

- Антипаттерн: eager create_managed_repo() в onboarding_controller.py msg_7 (WP-396)
- Исправление: lazy creation в setup_page + /start (web-flow)
