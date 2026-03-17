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
├── PACK-education                  ├── DS-my-strategy/
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

Общий контракт для любого L4-хранилища:

```typescript
interface KnowledgeBackend {
  search(query: string, embedding: number[], limit: number): Promise<SearchResult[]>
  ingest(docs: Document[]): Promise<void>
  listSources(): Promise<Source[]>
}
```

Реализации: `NeonBackend`, `SupabaseBackend`, `SqliteBackend`.

## 5. Связь с другими различениями

- **DP.D.031** (MCP Access Model): BYOB дополняет тир-модель — L4 MCP не проходит через Hub, а работает локально
- **DP.D.035** (Data Policy): BYOB = пользовательские данные не покидают его инфраструктуру, платформа не является data processor
- **DP.D.023** (4 контура): L2 Platform = платформенные данные, L4 Personal = BYOB

## 6. АрхГейт

ЭМОГССБ 9.1/10 (подробности: WP-73 §3.8.8).
