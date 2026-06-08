---
id: DP.M.298
name: "Fail-closed scope sidecar: ранний парсинг + deny при недоступности сервиса"
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: authorization
valid_from: 2026-06-08
related:
  see_also: [DP.FM.146]
tags: [authorization, sidecar, gateway, fail-closed, fail-open, scope-service, security, gke, multi-replica]
source: "session 2026-06-08, git diff gateway-mcp + bridge-scope-service (WP-402 Р1, report 2026-06-08-12)"
schema_version: 1
---

# DP.M.298 — Fail-closed scope sidecar

## Описание

Архитектурный паттерн разделения авторизационного enforcement на два компонента: gateway (точка входа) + scope-service (policy engine). Включает три инварианта безопасности.

## Три инварианта

**1. Ранний парсинг (early parse):** Gateway парсит аргументы запроса самостоятельно (например, `extractBridgeSourcePath`) и передаёт scope-сервису уже готовые поля `{source, path, hasConflict}`, а не сырой запрос. Это устраняет HTTP round-trip при синтаксически некорректных командах.

**2. Fail-closed:** Недоступность scope-service (5xx, таймаут) → gateway возвращает `deny`, а не `skip`. Инвариант безопасности важнее latency.

**3. Без per-pod кэша:** In-memory кэш в N-репликах (GKE и аналоги) не инвалидируется атомарно. 2 DB-запроса на path предпочтительнее stale-cache, который может пропустить отзыв доступа.

## Отличие от fail-open

Fail-open (skip при ошибке сервиса) скрывает неисправность и создаёт security hole: все запросы проходят, пока сервис недоступен.

## Применимость

- Authorization sidecar / policy enforcement point с SLA-чувствительностью
- Реплицированные окружения (replicas ≥ 2)
- Системы, где отзыв доступа должен срабатывать немедленно (не через TTL кэша)

## Тест

«При недоступности policy-engine — запрос проходит или блокируется?» Проходит → fail-open (нарушение). Блокируется → fail-closed (корректно).
