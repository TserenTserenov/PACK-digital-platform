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
last_updated: 2026-08-01
---

# DP.M.284 — Inline-cat дешевле --add-dir для LLM CLI с eager-индексацией

## Описание

CLI-инструменты на базе LLM (Claude CLI, Kimi CLI, Codex) принимают флаги доступа к директории (`--add-dir <path>`). Семантика отличается: одни делают **lazy attach** (файлы прикреплены, читаются по запросу), другие — **eager index** (рекурсивный обход + анализ при старте). При eager-индексации на больших директориях latency = 5+ мин/вызов и упирается в timeout.

Метод: вместо `--add-dir` — отфильтровать релевантные файлы (whitelist расширений `.md/.txt/.yaml/.json`) и конкатенировать содержимое **inline в prompt** через heredoc. Контент тот же, токены те же, но без overhead индексации.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Снижение latency ↔ сложность реализации | Inline-cat убирает 5+ минут eager-индексации, но требует ручного whitelist, heredoc и security-гарды в caller |
| Нативный CLI-attach ↔ контроль prompt | `--add-dir` прост в использовании, но фильтры и порядок контролирует CLI; inline-cat даёт caller полный контроль, но добавляет код |
| Безопасность ↔ удобство | CLI может иметь встроенный filter; inline-cat перекладывает sanitize/PII redact на caller, что повышает риск при спешке |
| Те же токены ↔ разный overhead | Контент одинаков; inline-cat экономит только индексацию, но если caller не фильтрует мусор, токены могут вырасти |
| Универсальность CLI ↔ специализация скрипта | `--add-dir` работает для любой директории; inline-cat требует знания структуры сессии и релевантных расширений |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| «Используем `--add-dir`, потому что это нативная фича CLI» | Практикующий доверяет CLI-инструменту, даже если eager index даёт 5+ минут задержки; нативность воспринимается как правильность |
| «Inline-конкатенация — костыль» | Cat через heredoc кажется хаком, хотя семантически эквивалентно; latency выигрыш отбрасывается из-за эстетики |
| «Whitelist и санитизация — лишняя работа» | При спешке пропускаются security guards, и в prompt попадает PII, бинарные файлы или мусор |
| «Если CLI поддерживает `--add-dir`, он оптимизирует индексацию» | Предположение, что tool сам обрабатывает директорию эффективно, без измерения реального старта |
| «Измеряем latency только после первого вызова» | Холодный старт с eager index игнорируется; реальная автоматизация страдает от timeout при каждом вызове |

## Источник

git diff DS-my-strategy commit d5be91a9e (kimi-peer-adapter.sh: «inline session files instead of --add-dir»), WP-356 peer-сессия 13.
