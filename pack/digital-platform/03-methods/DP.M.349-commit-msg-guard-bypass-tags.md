---
id: DP.M.349
title: "commit-msg guard with bypass-tag support via diff-filter=AR"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-04
source: current/docs audit Claude + Kimi (2026-06-04), git diff DS-my-strategy (554e69c45, 79d0458ba, .claude/hooks/commit-msg)
related:
  see_also: [DP.M.269, DP.D.180]
---

# DP.M.349 — commit-msg guard with bypass-tag

## Описание

Guard, защищающий директории от нежелательных файлов с поддержкой opt-out через метку в commit message, реализуется как **commit-msg хук** (не pre-commit).

## Принцип

**Проблема pre-commit:** хук запускается до написания сообщения коммита — bypass-токен (`[allow:current]`, `[no-registry-touch]`) ещё не существует → opt-out невозможен.

**commit-msg решение:** хук получает путь к файлу с уже написанным сообщением → читает токен → при наличии — пропускает guard.

## diff-filter=AR

Стандартный `--diff-filter=A` ловит только Added файлы. `git mv somewhere/file.md protected/` имеет статус **Renamed** (R) → файл проходит мимо стандартного guard.

**Правильно:** `--diff-filter=AR` = Added + Renamed → закрывает оба вектора.

## IPO

**Input:** файл сообщения коммита (путь передаётся в $1), `git diff --cached --name-only --diff-filter=AR`
**Process:** проверка защищённых путей → если bypass-токен в сообщении — пропуск; иначе — abort + hint
**Output:** 0 (OK) или 1 (abort с объяснением)

## Применение

1. Определить защищённые директории (`current/`, `docs/`, `archive/`)
2. Реализовать хук в `.claude/hooks/commit-msg` (не `.git/hooks/pre-commit`)
3. Документировать bypass-токены в CLAUDE.md / README
4. Тестировать: `git mv external.md current/ && git add -p && git commit -m "test"` → должен блокировать

## Граница

Bypass-токен — легитимный escape hatch, не обход безопасности. Аудит использования bypass: подсчёт в Week Close → >2/неделю = сигнал для расследования.

## Связи

- DP.M.269: bidirectional registry drift guard (дополняющий guard без bypass)
- DP.D.180: generated-vs-live-file-criterion (что именно защищаем)
- WP-7 peer-session 33 (2026-06-04): реализация в DS-my-strategy
