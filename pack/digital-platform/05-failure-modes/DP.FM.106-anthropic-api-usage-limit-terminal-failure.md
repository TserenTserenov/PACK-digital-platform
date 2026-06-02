---
id: DP.FM.106
name: "Anthropic API usage limit — терминальный blocker automation-pipeline"
type: failure-mode
domain: digital-platform
pack_refs: []
status: active
valid_from: 2026-05-29
schema_version: 1
source: "session-transcript 2026-05-29, peer-session 02 tsekh-errors-analysis"
---

# DP.FM.106 — Anthropic API usage limit как терминальный failure mode

## Описание

Исчерпание лимита Anthropic API (usage limit) — **терминальный** failure mode, не transient. Retry-цикл бесполезен до сброса лимита (обычно начало следующего billing period). Automation-pipeline молча деградирует или циклит, если нет явного детектора терминального отказа.

## Симптом

Все вызовы к Anthropic API возвращают HTTP 400 с телом, содержащим "usage limits" / "regain access". Pipeline продолжает работать (процесс жив), но все задачи, требующие LLM, тихо зависают или падают без алерта.

## Различение

| | Transient fail | Терминальный fail |
|---|---|---|
| Код | 5xx (сеть, перегрузка) | 400 (лимит исчерпан) |
| Тело ответа | Overloaded / Retry-after | "usage limits" / "regain access" |
| Retry | Помогает (exponential backoff) | Бесполезен до сброса лимита |
| Действие | Exponential backoff | Early-exit + алерт |

## Детектор (Anthropic-specific)

| Шаг | Проверка |
|-----|----------|
| 1 | Перехватить HTTP 400 (не 429) |
| 2 | Проверить тело ответа на подстроки: `usage limits`, `regain access`, `billing` |
| 3 | При совпадении → классифицировать как `AnthropicUsageLimitError` |
| 4 | Instant early-exit из retry-loop (не backoff) |
| 5 | Telegram-алерт с датой восстановления лимита (начало следующего billing period) |

## Тест

«Восстановится ли при ретрае через 30 мин?» Нет → терминальный failure mode.

## Scope

**Anthropic API only.** Другие провайдеры (OpenAI, Gemini) возвращают 429 при rate limit — это transient fail с retry-after. Не смешивать detection logic.

## Связи

- DP.FM.105 — Internal probe blind to own failure (родственный: pipeline жив, но бесполезен)
- DP.M.214 — Silent OAuth token provisioning (родственный: молчаливая деградация по auth)
