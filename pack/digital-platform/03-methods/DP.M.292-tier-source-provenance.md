---
id: DP.M.292
type: method
title: "tier_source — поле провенанса в multi-source авторизации"
slug: tier-source-provenance
domain: digital-platform
trust: high
epistemic_stage: observed
valid_from: 2026-06-07
source: session-transcript 2026-06-07, WP-349 Ф26; commit DS-MCP ef16228 (gateway-mcp/src/index.ts)
related: []
schema_version: 1
---

# DP.M.292 — tier_source: поле провенанса в multi-source авторизации

## Описание

При multi-source авторизации (JWT-клейм → БД → fallback) к авторизационному ответу добавляется поле провенанса `<attr>_source`, указывающее откуда получен атрибут.

## Паттерн

```typescript
type TierSource = "claim" | "persona_db" | "fallback";

interface AuthResult {
  tier: TierLevel;
  tierSource: TierSource;
  // ...
}
```

## Применимость

«Атрибут пользователя (тир/роль/квалификация) может прийти из нескольких источников с разным уровнем доверия?» → добавить `<attr>_source` поле провенанса к ответу.

## Ценность

Позволяет отлаживать неожиданные fallback-ситуации без изменения бизнес-логики. Без провенанса: видим значение "T1" — не знаем, это клейм или дефолт. С провенансом: "T1" + `tierSource: "fallback"` → понятно, что авторизация деградировала.

## Связи

- Применяется в: gateway-mcp/src/index.ts (WP-349 Ф26)
- Обобщает: AuthResult pattern при multi-source атрибутах
