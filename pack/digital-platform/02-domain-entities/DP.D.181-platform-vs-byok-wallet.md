---
id: DP.D.181
name: "Платформенный LLM-кошелёк ≠ личный BYOK-кошелёк"
type: distinction
pack: PACK-digital-platform
domain: digital-platform / billing
trust: confirmed
epistemic_stage: observed
valid_from: 2026-06-09
source: "session-transcript 2026-06-09 + git diff DS-my-strategy (inbox/WP-400/WP-400.md, commit 4d3b92ca1)"
see_also: [DP.IWE.003, DP.ARCH.002]
schema_version: 1
---

# DP.D.181 — Платформенный LLM-кошелёк ≠ личный BYOK-кошелёк

## Различение

В IWE сосуществуют два типа LLM-расходов:

- **Платформенный кошелёк:** ключи зашиты в инфраструктуру (LiteLLM, Cloudflare Workers, gateway), расход списывается с платформенного аккаунта, учёт через Usage API, покрывает всех пользователей платформы.
- **Личный (BYOK) кошелёк:** ключ пользователя, прямые вызовы (Hermes в VS Code, Premium-функции), расход на счёт пользователя.

## Граница

Один и тот же Hermes меняет кошелёк в зависимости от канала вызова: Hermes личный (VS Code) = личный кошелёк; Hermes через gateway = платформенный кошелёк.

## Применение

При аудите расходов: не смешивать ключи, не выносить в DoD WP-400 ключи, которые технически несовместимы с LiteLLM (например, эмбеддинги с низкой latency → прямой вызов OpenAI, учёт отдельно).

## Связи

- DP.IWE.003 (gateway architecture) — упоминает BYOK-management (`list/grant/revoke_llm_key`), но не формулирует само различение
- DP.ARCH.002 (service tiers) — упоминает BYOK как T4 Direct MCP опцию, тот же контекст

## Источник

session-transcript 2026-06-09; git diff DS-my-strategy (inbox/WP-400/WP-400.md, commit 4d3b92ca1)
