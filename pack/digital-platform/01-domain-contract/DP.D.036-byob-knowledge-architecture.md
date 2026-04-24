---
id: DP.D.036
name: BYOB Knowledge Architecture
type: distinction
status: draft
summary: "Различение BYOB (Bring Your Own Backend) vs Managed: данные пользователя хранятся на его ресурсах, платформа даёт код и L2-знания. Связано с MCP Hub (ADR-018 v2) и контурами L2/L4."
created: 2026-03-17
trust:
  F: 3
  G: domain
  R: 0.6
epistemic_stage: emerging
related:
  refines: [DP.ARCH.001]
  uses: [DP.D.031, DP.D.035]
  realized_by: [ADR-IWE-003]
---

# BYOB Knowledge Architecture (DP.D.036)

## 1. Различение

| | BYOB (Bring Your Own Backend) | Managed (Multi-tenant) |
|---|---|---|
| **Данные L4** | На ресурсах пользователя (его Neon/Supabase/SQLite) | На ресурсах платформы (общий Neon, tenant_id) |
| **GDPR** | Пользователь отвечает за свои данные | Платформа отвечает за все данные |
| **Trust** | Нулевое доверие к платформе (данные не уходят) | Пользователь доверяет платформе |
| **Масштаб** | Горизонтальный (каждый на своём) | Вертикальный (платформа растит инфраструктуру) |
| **Обучаемость** | Нужно завести DB-аккаунт | Ничего не нужно |
| **Стоимость для платформы** | Нулевая (хранение — пользователя) | Растёт с числом пользователей |

## 2. Решение для IWE

**BYOB** — выбранная архитектура для Knowledge Gateway (ADR-018 v2).

> **Терминология:** «Gateway» — архитектурный паттерн (DP.IWE.003). Инстанс паттерна — **Aisystant MCP** (`mcp.aisystant.com`).

Причины:
1. Pack пользователя = **конфиденциальное доменное знание** (его бизнес). Хранение на чужом сервере = trust barrier
2. Платформа не хочет нести GDPR-ответственность за пользовательские данные
3. Горизонтальный масштаб бесплатно — каждый пользователь оплачивает (или не оплачивает, если SQLite) своё хранилище
4. Local-first = SOTA-паттерн (Context Engineering: Select + Compress)

## 3. Архитектура контуров

```
L2 Platform (remote, наш)          L4 Personal (BYOB, пользователя)
├── ZP, FPF, SPF                    ├── PACK-my-domain-1/
├── PACK-digital-platform           ├── PACK-my-domain-2/
├── PACK-MIM                  ├── DS-my-strategy/
├── PACK-MIM                        └── DS-my-projects/
├── PACK-personal
├── courses, guides
└── FMT-docs

         └──────────┬──────────┘
                    │
          Knowledge Gateway (локальный)
          search(query) → fan-out L2 + L4 → merge by score
                    │
                    ▼
          Один MCP-endpoint для клиента
```

## 4. Backend Interface

> **Реализационная спецификация:** [ADR-IWE-003](../../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/System-Implementations/ADR-IWE-003-gateway-backend-interface.md) — полный контракт MCP-серверов за Gateway, Knowledge Gate, pipeline подключения.

Общий контракт для любого backend MCP за Gateway:

```typescript
// Обязательный: MCP JSON-RPC (initialize, tools/list, tools/call, ping)
// Обязательный инструмент: search(query, limit?) → SearchResult[]
// SearchResult: { filename, source, score: [0,1], content_preview }
```

**Три текущие реализации:**

| Backend | Контур | Хранилище | RLS |
|---------|--------|-----------|-----|
| knowledge-mcp | L2 Platform | Neon pgvector | Нет (публичное) |
| personal-knowledge-mcp | L4 Personal | Neon pgvector | `WHERE user_id` (JWT) |
| digital-twin-mcp | L2 per-user | Neon (реляционное) | `WHERE user_id` (JWT) |

**Будущие реализации (BYOB):** пользователь деплоит свой MCP (Supabase pgvector, SQLite + vec0) → проходит Knowledge Gate → подключается к Gateway.

## 5. Связь с другими различениями

- **DP.D.031** (MCP Access Model): BYOB дополняет тир-модель — L4 MCP не проходит через Hub, а работает локально
- **DP.D.035** (Data Policy): BYOB = пользовательские данные не покидают его инфраструктуру, платформа не является data processor
- **DP.D.023** (4 контура): L2 Platform = платформенные данные, L4 Personal = BYOB

## 6. АрхГейт

ЭМОГССБ 9.1/10 (подробности: WP-73 §3.8.8).
