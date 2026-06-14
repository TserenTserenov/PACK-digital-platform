---
id: DP.SOTA.030
title: "EAM (EightX Agent Manifest) — внешний стандарт описания AI-агентов"
type: sota
domain: digital-platform
status: draft
valid_from: 2026-06-14
sources:
  - EightX Labs, EAM v0.1 (github.com/jshipman888/eam-spec, Apache 2.0)
applicability:
  - WP-337 (DP.ROLE runtime_compat field)
  - PACK-digital-platform / 02-domain-entities / agent-roles (шаблон DP.ROLE)
---

# EAM (EightX Agent Manifest) — внешний стандарт описания AI-агентов

## Феномен

EAM — открытый YAML-стандарт описания AI-агентов от EightX Labs (Apache 2.0). Поля: `agent` (identity), `skills[]` (capabilities + inputs/outputs), `runtime` (provider compatibility), `policies` (PII, human-review, spending cap), `connectors[]` (MCP-серверы), `subagents[]`.

## Соответствие IWE

| EAM | IWE-аналог | Статус |
|-----|-----------|--------|
| `skills[]` | DP.SC (Service Clause) | IWE богаче: trigger, invariant, failure mode |
| `agent` (identity, category) | DP.ROLE | Близкое соответствие |
| `policies.human_review_required` | WP Gate / AR.NNN | Близкое соответствие |
| `connectors[]` | MAP.002 + executor-catalog.yaml | Близкое соответствие |
| `runtime` (provider matrix) | **ОТСУТСТВУЕТ в DP.ROLE** | **Gap** |

## Gap: отсутствие runtime_compat в DP.ROLE

EAM `runtime.preferred / supported / degraded / unsupported` задаёт явную матрицу совместимости конкретной роли с LLM-провайдерами/моделями. В IWE модельный тир глобальный (distinctions.md, Model Tiering) — нет per-role matrix.

**Следствие:** нельзя декларативно сказать «DP.ROLE.042 Диагност — preferred: Sonnet, degraded: Haiku, unsupported: GPT-3.5».

**Actionable:** добавить опциональное поле `runtime_compat` в шаблон DP.ROLE (PACK-digital-platform). Задача для WP-337.

## Что НЕ брать из EAM

marketplace/passport/monetization — это agnt8x B2B-платформа, не IWE.

## Вывод

EAM независимо подтверждает архитектурные решения IWE (DP.ROLE, DP.SC, WP Gate), что повышает confidence в подходе. Одновременно выявляет конкретный пробел (runtime_compat), устранимый добавлением одного поля в шаблон.
