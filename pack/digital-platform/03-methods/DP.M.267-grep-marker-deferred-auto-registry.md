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
last_updated: 2026-08-01
---

# DP.M.267 — Grep-marker deferred auto-registry

## Описание

Метод автоматической сборки реестра отложенных решений из WP-context файлов через grep текстовых маркеров. Устраняет дрейф между ручным реестром и реальным состоянием WP-файлов.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Ручной реестр ↔ derived реестр | Ручной реестр структурирован и читаем, но устаревает за 2-3 дня активной разработки; derived через grep актуален, но размывает структуру и формат |
| Разнообразие маркеров ↔ стандартизация | Много синонимичных маркеров ловит больше случаев, но усложняет линтер и повышает false positives при grep |
| Актуальность ↔ читаемость текста | Co-located маркер гарантирует актуальность реестра, но загромождает текст WP-context и может мешать чтению |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| «Реестр проще поддерживать вручную» | Практикующий предпочитает ручной реестр, недооценивая скорость устаревания при активной разработке и drift к source files |
| Добавление новых маркеров «на всякий случай» | Размывание маркерной грамматики усложняет скрипт, повышает false positives и делает derived-реестр менее читаемым |
| Обработка маркера без `[promoted YYYY-MM-DD]` | Строка остаётся в derived-реестре, потому что маркер не убрали и не отметили дату, создавая иллюзию открытого deferred-решения |

## Связи

- Реализация: `DS-my-strategy/scripts/generate-spin-off-registry.sh`
- Выход: `DS-my-strategy/current/spin-off-registry.md`
- Смежный: DP.M.269 (bidirectional drift guard для принудительной синхронизации через git hook)
