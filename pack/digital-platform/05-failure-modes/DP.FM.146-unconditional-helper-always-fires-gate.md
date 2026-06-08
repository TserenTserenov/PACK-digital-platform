---
id: DP.FM.146
name: "Unconditional helper return = always-fires gate: гейт срабатывает для всех пользователей"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: authorization
severity: critical
valid_from: 2026-06-08
related:
  see_also: [DP.FM.126]
tags: [authorization, feature-gate, tier-check, javascript, typescript, conditional-guard, always-true]
source: "session 2026-06-08, git diff gateway-mcp CF Worker (8f62c6fb3, create_repository handler, WP-5 Ф-30)"
schema_version: 1
---

# DP.FM.146 — Unconditional helper return = always-fires gate

## Описание

Helper-функция для формирования объекта ошибки (например, `makeTierError`) создана для создания error-объекта, но не проверяет условие — всегда возвращает non-null. Вызывающий код `if (errorHelper())` всегда truthy → gate блокирует всех пользователей без исключения.

## Контекст возникновения

- Feature-gate код на JS/TS (или любом языке с truthy/falsy semantics)
- Helper-функция разработана для создания сообщений об ошибках, а не для проверки условий
- Разработчик предполагает, что helper вернёт null/undefined при «нет ошибки», но реализует его как factory

## Симптом

Самопротиворечивое сообщение об ошибке: «Недоступно на вашем тире T3, требуется T3» (пользователь уже на нужном тире, но блокирован). Функция срабатывает для всех пользователей независимо от тира.

## Корректные паттерны

**Вариант A:** helper возвращает null при соответствии условию:
```typescript
function makeTierError(requiredTier: Tier, userTier: Tier): TierError | null {
  if (userTier >= requiredTier) return null; // нет ошибки
  return { message: `Требуется ${requiredTier}, у вас ${userTier}` };
}
// вызов:
const tierError = makeTierError(requiredTier, userTier);
if (tierError) return tierError;
```

**Вариант B:** явная проверка условия до вызова helper:
```typescript
if (userTier < requiredTier) {
  return makeTierError(requiredTier, userTier);
}
```

## Диагностика

«Самопротиворечивое сообщение об ошибке» (например, "требуется T3, у вас T3") — сигнал двойной проверки для conditional-guard функций.

## Тест

«Helper-функция gate'а всегда возвращает non-null?» Да → always-fires gate bug.
