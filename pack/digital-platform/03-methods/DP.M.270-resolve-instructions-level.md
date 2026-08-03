---
id: DP.M.270
title: "Tiered MCP instructions via resolveInstructions(level)"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-03
source: peer-session 2026-06-03-13, WP-394 Ф4.1, git diff gateway-mcp 962820d3
last_updated: 2026-08-01
---

# DP.M.270 — Tiered MCP instructions via resolveInstructions(level)

## Описание

Паттерн для MCP-инструмента `get_instructions`: вместо одного монолитного системного промпта — три уровня детализации, выбираемые потребителем через параметр `level`.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Полнота инструкций ↔ token budget | Full prompt даёт все правила, но потребляет токены; compact/experienced экономят, но рискуют пропустить edge-case, важный для конкретного агента |
| Backward compatibility ↔ эволюция | Default `full` сохраняет старых клиентов, но замедляет внедрение tiering; опциональный `level` требует дисциплины и документирования контракта |
| Унификация уровней ↔ индивидуальность агента | Три фиксированных уровня проще поддерживать, но не учитывают специфику каждого агента; кастомизация уровней усложняет контракт и тестирование |

## IPO

**Input:** `level: "full" | "compact" | "experienced"` (опционален, backward-compatible)
**Process:** `resolveInstructions(level)` — switch по level + fallback на full при неизвестном значении
**Output:** системный промпт нужной длины для конкретного типа агента

## Уровни

| Уровень | Размер | Целевой потребитель |
|---------|--------|---------------------|
| `full` | ~15K | Claude Code (все детали, полный контекст) |
| `compact` | ~3K | Hermes/headless агенты (сжатый, без примеров) |
| `experienced` | ~1K | Kimi, опытные агенты (только ключевые правила) |

## Реализация

```typescript
const IWE_SYSTEM_PROMPT_COMPACT = "..."; // ~3K
const IWE_SYSTEM_PROMPT_EXPERIENCED = "..."; // ~1K

function resolveInstructions(level?: string): string {
  switch (level) {
    case "compact": return IWE_SYSTEM_PROMPT_COMPACT;
    case "experienced": return IWE_SYSTEM_PROMPT_EXPERIENCED;
    default: return IWE_SYSTEM_PROMPT_FULL; // fallback = full
  }
}
```

## Применение

При добавлении нового агента-потребителя: определить его профиль → выбрать уровень → передать `level` в вызове `get_instructions`. Параметр опционален — старые клиенты получают `full` по умолчанию (backward-compatible).

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| «Full — безопаснее, всегда full» | Практикующий предпочитает full для всех агентов, не экономя токены и не проверяя, нужен ли уровень конкретному потребителю |
| Игнорирование fallback | Новый уровень добавляется без default fallback на full, и неизвестный или отсутствующий level ломает клиента или возвращает пустой prompt |
| «Compact = experienced» | Склонность считать, что сжатый prompt одинаково хорош для всех headless-агентов, размывая разницу между Hermes и Kimi |

## Связи

- Источник: DP.SC.NNN (get_instructions service clause)
- Применено: gateway-mcp ветка `wp-394-tiering`
