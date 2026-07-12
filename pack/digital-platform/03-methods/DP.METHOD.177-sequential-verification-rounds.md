---
id: DP.METHOD.177
name: "Последовательные раунды верификации с изоляцией контекста"
type: method
pack: PACK-digital-platform
domain: digital-platform / verification
kind: Method
status: active
created: 2026-07-12
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-07
sources:
  - "WP-5 MCP tool discovery, session-close 2026-07-07 (DP.SC.129 ArchGate + 3 пир-сессии)"
related:
  complements: [DP.METHOD.173, DP.M.066, DP.M.067]
schema_version: 1
---

# DP.METHOD.177 — Последовательные раунды верификации с изоляцией контекста

## Определение

Метод верификации security-critical реализаций: минимум **3 последовательных раунда** с контекстной изоляцией между раундами. Каждый раунд верифицирует фикс предыдущего. Верификатор раунда N не видит результаты раундов 1..N-1.

## IPO

- **Вход:** реализованный артефакт (код, конфиг, workflow)
- **Процесс:** 3+ раунда с изолированными верификаторами; раунд N фиксирует дефект → раунд N+1 подтверждает фикс
- **Выход:** цепочка раунд→фикс→раунд с фиксацией найденных/исправленных дефектов

## Свойства метода

1. **Sequential (не parallel):** параллельные субагенты — для первичного обнаружения без предыдущих результатов. Последовательные раунды — для итеративного исправления (каждый round видит только свой scope, не accumulated findings).
2. **Context isolation:** верификатор раунда N не видит выводы раундов 1..N-1 → предотвращает confirmation bias (subagent думает «предыдущий уже проверил»).
3. **Minimum 3 rounds для security-critical:** 2 раунда — недостаточно (накапливается confirmation bias, bypass может уцелеть).

## Когда применять

- Security-critical реализации (auth, validation, access control)
- Артефакты, где bypass проходит при поверхностной проверке (e.g., validation bypass)
- После ArchGate: ArchGate + 1 pass ≠ достаточная верификация

## Различение с DP.METHOD.173 и другими

| Метод | Вопрос | Ось |
|-------|--------|-----|
| DP.M.066 Multi-round | Как искать? (сужение scope) | Thoroughness |
| DP.M.067 Two-pass | Что сравнивать? (cold vs warm) | Perspective diversity |
| DP.METHOD.173 Context-isolated | Откуда читать? (внешние факты) | Source independence |
| **DP.METHOD.177 Sequential rounds** | **Как последовательно устранять?** | **Confirmation bias elimination** |

Все четыре метода ортогональны и могут применяться совместно.

## Источник

WP-5 MCP tool discovery: ArchGate + первичная реализация (Б.x validation + Л2.2 audit log) — 2 пир-сессии. Validation bypass обнаружен только на третьем круге с контекстной изоляцией.
