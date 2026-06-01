---
id: DP.M.232
name: "Декомпозиция umbrella-РП: domain-specific subsystem ≠ standard infra direction"
type: method
domain: digital-platform
pack_refs: []
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "peer-сессия 2026-05-29-14-mim-as-it-company Тема 4 (ALE как направление WP-7)"
---

# DP.M.232 Декомпозиция umbrella-РП: domain-specific subsystem ≠ standard infra direction

## Описание

При декомпозиции umbrella-РП на направления отличать **стандартные инфраструктурные направления** (CI/CD, observability, deployment, package management — общие для любой production-системы) от **domain-specific subsystems** (компоненты, отсутствующие в reference-IT-стеке для данного класса систем: reflection loop, user-model store, calibration engine для knowledge/agent-платформ).

## IPO

**Вход:** umbrella-РП с N кандидатов в направления; reference-стек для класса систем (например, типовая SaaS-платформа).
**Процесс:**
1. Для каждого кандидата задать тест: «есть ли эта подсистема в reference-стеке для класса систем X?»
2. **Нет** → выделять как отдельное domain-specific направление с собственными дочерними РП.
3. **Да** → инфра-направление, агрегировать с другими стандартными.

**Выход:** карта направлений umbrella-РП с явным разделением domain-specific и infra.

## Тест применимости

- Reflection loop в knowledge-системе → domain-specific (не растворять в observability).
- User-model store с cp/bh-профилем → domain-specific (не растворять в storage).
- CI/CD pipeline → infra.
- Logging/metrics → infra.

## Анти-паттерн

Domain-specific subsystem растворяется в process-funnel (например, ALE в Loop F = «процессный funnel производства»). Разные масштабы: Loop F — процесс производства артефактов; ALE — подсистема обучения платформы. Слияние теряет видимость roadmap для domain-specific части.

## Применение

При декомпозиции зонтичного РП (WP-7, WP-150, любой umbrella с N≥3 направлений). Применимо к knowledge/agent системам, где user-modeling / feedback-loop / adaptive-calibration — не add-on, а суть продукта.
