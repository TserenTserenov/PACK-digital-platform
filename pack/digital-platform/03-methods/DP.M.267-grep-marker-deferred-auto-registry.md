---
id: DP.M.267
title: "Grep-marker based auto-registry for deferred decisions in WP-context files"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-02
source: WP-377 Ф1.6, git commit 6de606a5 DS-my-strategy (generate-spin-off-registry.sh + current/spin-off-registry.md)
---

# DP.M.267 — Grep-marker deferred auto-registry

## Описание

Метод автоматической сборки реестра отложенных решений из WP-context файлов через grep текстовых маркеров. Устраняет дрейф между ручным реестром и реальным состоянием WP-файлов.

## Алгоритм

1. При откладывании решения в WP-context файле поставить явный текстовый маркер рядом с контекстом:
   - `spin-off`, `отложено`, `deferred`, `отдельный РП`, `child WP`
2. Bash-скрипт grep'ит все `inbox/WP-*.md` по этим маркерам:
   ```bash
   grep -rn "spin-off\|отложено\|deferred\|отдельный РП\|child WP" inbox/WP-*.md
   ```
3. Выход: markdown-таблица `| WP-ID | файл:строка | маркер | контекст 120 символов |`
4. Регенерация — один запуск скрипта (идемпотентна)

## Цикл ревью

- Month Close + перед Strategy Session → запустить скрипт, просмотреть таблицу
- Обработанная строка: убрать маркер ИЛИ добавить `[promoted YYYY-MM-DD]`

## Ключевые инварианты

- **Маркеры co-located с контекстом решения** — drift между реестром и источником невозможен
- **Реестр полностью derived** → перегенерируется, не редактируется вручную
- Альтернатива ручного реестра: дорого поддерживать, устаревает за 2-3 дня активной разработки

## Применимость

Любая governance-система с отложенными решениями в source-files:
- WP-context files со spin-off'ами
- Architecture Decision Records с defer-кандидатами
- Backlog-файлы с `// TODO: WP Gate`

## Связи

- Реализация: `DS-my-strategy/scripts/generate-spin-off-registry.sh`
- Выход: `DS-my-strategy/current/spin-off-registry.md`
- Смежный: DP.M.269 (bidirectional drift guard для принудительной синхронизации через git hook)
