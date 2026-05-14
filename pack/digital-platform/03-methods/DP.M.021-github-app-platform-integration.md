---
id: DP.M.021
name: GitHub App Platform Integration
type: method
domain: platform-integration
created: 2026-05-11
source: WP-301 v3
epistemic_stage: validated
trust: high
---

# DP.M.021 — GitHub App для платформенной интеграции с пользовательскими репо

## Проблема

Manual webhook не масштабируется на 50+ пользователей: ручная регистрация, общий HMAC-секрет (уязвимость), нет самообслуживания при уходе пользователя.

## Метод

Платформа регистрирует GitHub App один раз. Каждый пилот ставит App на свой репо (один клик Install). App получает write-permission и push events. Каждый installation_id уникален → per-installation user mapping. Уход пилота = uninstall (самообслуживание).

## Инварианты

1. Платформа регистрирует App один раз — не per-user
2. Каждый installation_id уникален → изолированный user mapping
3. Write-permission выдаётся по согласию пользователя (Install-flow)
4. Uninstall = автоматический отзыв доступа (без ручных шагов платформы)

## Применимость

Любая платформа, интегрирующаяся с GitHub-репозиториями множества пользователей (50+).

## Связи

- WP: WP-301 (SC.020 delivery, архитектурная поправка v3)
- AS.M.010 — GitHub App installation token (auth-механизм)
- AS.FM.021 — org vs personal scope (типичная ошибка установки)
