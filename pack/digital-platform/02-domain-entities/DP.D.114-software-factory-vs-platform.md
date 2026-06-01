---
id: DP.D.114
name: "Software factory ≠ Platform — single-product vs PaaS"
type: distinction
domain: digital-platform
pack_refs: []
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "peer-сессия 2026-05-29-14-mim-as-it-company (Kimi-писатель + Claude-напарник), post-close naming review WP-364"
---

# DP.D.114 Software factory ≠ Platform

## Различение

| | Platform | Software factory |
|---|----------|------------------|
| **Продукт** | Множество продуктов разных tenants | Один тип артефакта (in-house) |
| **Потребители** | Third-party разработчики | Внутренние команды |
| **Семантика** | PaaS / IaaS / Aisystant как платформа разработки агентов | Конвейер промышленного производства одного типа изделий |
| **Зонтик объединяет** | Self-service инструменты для tenants | N направлений-конвейеров (CI/CD, observability, content-pipeline, ALE, …) |
| **Пример** | Aisystant (платформа разработки агентов) | Фабрика руководств МИМ (производство руководств) |

## Тест

«Есть ли third-party клиенты, использующие эту инфраструктуру для **своих** продуктов?»
- Да → Platform
- Нет, один тип артефакта on-prem → Software factory

## Применение

Зонтичный РП ≥10 направлений, объединяющий производство одного типа артефактов → имя «Фабрика X», не «Платформа X». Свёртывание в «платформу» приглашает PaaS-семантику и third-party governance-требования, не нужные in-house factory.

## Связь с S-13 (имя РП = существительное-артефакт)

«Фабрика» соблюдает правило (существительное-артефакт домена); глаголы «производство / доставка / издание / выпуск» — нарушение (см. feedback_wp_naming_verbs.md).

## Антипаттерн

Именовать single-product зонтичный РП «Платформа X» по аналогии с cloud-провайдерами → ожидание PaaS-возможностей (multi-tenant isolation, billing-per-tenant, SDK для third-party) → масштаб не соответствует, governance вырождается в imitation.
