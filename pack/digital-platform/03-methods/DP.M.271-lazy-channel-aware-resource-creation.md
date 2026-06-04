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
---

# DP.M.271 — Lazy channel-aware resource creation in multi-channel onboarding

## Описание

В multi-channel системах (бот + web) ресурсы создаются lazily в том канале, который их требует — не eager при первом onboarding-событии в любом канале.

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

## Связи

- Антипаттерн: eager create_managed_repo() в onboarding_controller.py msg_7 (WP-396)
- Исправление: lazy creation в setup_page + /start (web-flow)
