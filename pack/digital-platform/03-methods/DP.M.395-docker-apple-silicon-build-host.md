---
id: DP.M.395
name: "--build-host обязателен при деплое Docker-образа с Apple Silicon в Railway"
kind: Method
status: active
domain: digital-platform
type: infrastructure-rule
created: 2026-07-23
source: "WP-170 batch 5, capture #102 — принято пилотом в PACK-digital-platform (граница Pack: инфраструктура платформы, не поведение агента)"
wp: WP-170
---

# DP.M.395 --build-host обязателен при деплое Docker-образа с Apple Silicon в Railway

## Правило

При деплое Docker-образа, собранного на Apple Silicon (arm64), в Railway (linux/amd64) — флаг `--build-host` обязателен. Без него сборка образа идёт под архитектуру хоста сборки, несовместимую с рантаймом Railway.

## Почему в DP, не в AS

Это инфраструктурное правило деплоя платформы (конкретная CLI-опция для конкретной комбинации архитектур), а не паттерн поведения автономного агента — граница `PACK-autonomous-agents` (про агентов) / `PACK-digital-platform` (про платформу) здесь однозначна.
