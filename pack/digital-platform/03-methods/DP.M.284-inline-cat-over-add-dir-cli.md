---
id: DP.M.284
title: "Inline-cat дешевле --add-dir для LLM CLI с eager-индексацией"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-05
source: git diff DS-my-strategy commit d5be91a9e (kimi-peer-adapter.sh perf fix), WP-356 промоция
---

# DP.M.284 — Inline-cat дешевле --add-dir для LLM CLI с eager-индексацией

## Описание

CLI-инструменты на базе LLM (Claude CLI, Kimi CLI, Codex) принимают флаги доступа к директории (`--add-dir <path>`). Семантика отличается: одни делают **lazy attach** (файлы прикреплены, читаются по запросу), другие — **eager index** (рекурсивный обход + анализ при старте). При eager-индексации на больших директориях latency = 5+ мин/вызов и упирается в timeout.

Метод: вместо `--add-dir` — отфильтровать релевантные файлы (whitelist расширений `.md/.txt/.yaml/.json`) и конкатенировать содержимое **inline в prompt** через heredoc. Контент тот же, токены те же, но без overhead индексации.

## Принцип

| Подход | Старт CLI | Контент в prompt | Безопасность |
|--------|-----------|-------------------|--------------|
| `--add-dir <session-dir>` | eager index 5+ мин | автоматически через attachment | filter ограничен CLI |
| inline-cat через heredoc | мгновенный | явно через `cat <whitelist>` | sanitize + PII redact + .agentignore controlled by caller |

Inline-режим даёт ~5× ускорение при том же объёме контекста.

## Применение

При построении peer-adapter, sub-agent dispatcher или любого автоматического вызова LLM CLI с динамическим контекстом:

1. Whitelist расширений: `.md .txt .yaml .json` (исключить `.png .pdf .zip`).
2. Сборка prompt'а:
   ```bash
   prompt="$user_prompt

   --- SESSION FILES ---
   $(cat "$session_dir"/*.{md,yaml,json} 2>/dev/null)
   "
   ```
3. Передать prompt одним аргументом, без `--add-dir`.
4. Security guards применять перед конкатенацией: sanitize, PII redact, .agentignore filter.

## Тест применимости

«CLI делает обход директории при старте (eager index)?» Да + директория >50 файлов → inline дешевле. Если CLI lazy-attach (Claude CLI) → `--add-dir` норм, ускорение незначительно.

## Источник

git diff DS-my-strategy commit d5be91a9e (kimi-peer-adapter.sh: «inline session files instead of --add-dir»), WP-356 peer-сессия 13.
