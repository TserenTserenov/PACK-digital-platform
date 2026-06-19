---
id: DP.FM.165
title: "Foreground Shell orphan при рестарте IDE Extension"
type: fm
pack: DP
tags: [foreground-shell, orphan-process, ide-extension, agent-execution, taskoutput]
status: draft
valid_from: 2026-06-16
schema_version: 1
---

# DP.FM.165 — Foreground Shell orphan при рестарте IDE Extension

## Описание

При рестарте VS Code Extension агентский процесс завершается, но серверные сессии сохраняются. Форграунд Shell, запущенный до рестарта, становится orphan — его elapsed time продолжает накапливаться с момента предыдущего запуска. После возобновления сессии агент видит «3465 секунд» и интерпретирует как зависший процесс.

## Условия возникновения

- Рантайм с session-server архитектурой (не завершает child processes при рестарте Extension)
- Команда выполняется через Foreground Shell (не TaskOutput / run_in_background=true)
- IDE Extension перезапускается во время выполнения команды

## Симптом

Агент видит elapsed time > timeout (например, 3465s) и сообщает о «зависшем процессе». Команда фактически завершилась (или никогда не запускалась) — это накопленное время orphan процесса.

## Fix

Использовать `run_in_background=true` + `TaskOutput` для операций >60s.

## Профилактика

Правило: операции >60s → `run_in_background=true` + `TaskOutput` (ограничение `agent_task_timeout_s=300`). Foreground Shell — только для быстрых (≤60s) команд.

Тест обнаружения: «может ли процесс-агент быть перезапущен рантаймом без завершения дочернего Shell-процесса?» Если да → Foreground Shell = риск orphan для операций >60s.

## Применимость

Любой агент в IDE с session-server архитектурой (kimi-for-coding, VS Code Extension и др.).

## Источник

session-transcript 2026-06-16; git commit 600f1e3 (AGENTS.md Shell vs TaskOutput rule)
