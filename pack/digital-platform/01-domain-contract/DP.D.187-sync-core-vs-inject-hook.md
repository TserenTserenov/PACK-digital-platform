---
id: DP.D.187
name: "SYNC-CORE (общее ядро инструкций) ≠ Claude-specific inject-hook"
type: distinction
domain: digital-platform
status: active
valid_from: 2026-07-05
schema_version: 1
source: "session-close 2026-07-03 (WP-450 Ф4, CLAUDE.md SYNC-CORE block)"
---

# DP.D.187 — SYNC-CORE ≠ Claude-specific inject-hook

| Аспект | SYNC-CORE | Claude-specific inject-hook |
|--------|-----------|---------------------------|
| **Что это** | Общее ядро инструкций (генерируется в AGENTS.md) | PreToolUse хук только для Claude (inject-code-style.sh) |
| **Потребители** | Claude, Kimi, Hermes — все агенты | Только Claude в CLI-рантайме |
| **Механизм доставки** | Текст в AGENTS.md (статический) | Hook перед каждым вызовом инструмента |
| **При оптимизации «под Claude»** | Нельзя убирать содержимое — у других агентов нет хука-замены | Можно менять — другие агенты не зависят |

## Инвариант

При оптимизации блока в SYNC-CORE для одного агента (например Claude): проверить, получат ли все другие потребители SYNC-CORE ту же информацию другим способом. Если хотя бы один нет — оптимизировать нельзя.

## Применение

При slim-рефакторинге CLAUDE.md (WP-450): предложение убрать блок Code Style из SYNC-CORE (потому что Claude получает его через inject-code-style.sh) — неверно. Kimi и Hermes читают только AGENTS.md (из SYNC-CORE) и inject-хука не имеют.

## Связи

- DP.D.058 (Service Clause ≠ Carrier) — структурный аналог: обещание ≠ механизм реализации
