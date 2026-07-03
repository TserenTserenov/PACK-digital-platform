---
id: DP.FM.170
title: "Guard с literal-паттерном строки коммита сломался в день добавления"
name_ru: "Literal guard pattern mismatch on day one"
name_en: "Literal commit guard pattern mismatch at initial deployment"
summary: "Guard проверяет наличие коммита через жёсткое совпадение текста, но реальные коммиты используют другой синтаксис. Guard сломан в момент деплоя."
type: failure-mode
pack: PACK-digital-platform
domain: digital-platform / automation-guards
severity: major
trust: confirmed
epistemic_stage: confirmed
valid_from: 2026-07-02
related:
  see_also: [DP.FM.031, AR.239]
source: "WP-455 + bug-fix, session-transcript 2026-07-02-01-day-open-guard-diagnosis"
---

# DP.FM.170 — Literal guard pattern mismatch on day one

## Суть паттерна

Guard проверяет наличие коммита (или другого артефакта) через **жёсткое совпадение текста** нескольких шаблонов. Реальные коммиты записаны в **другом синтаксисе** — с использованием скобок, другого регистра или другого разделителя. Guard ни разу не срабатывает корректно с момента добавления.

**Пример (источник):**
Guard ищет `"day-close: дата"` или `"Day Close дата"`, а реальные коммиты пишутся `"day-close(2026-07-02): ..."` — с круглыми скобками. Оба шаблона не совпадают ни с одним реальным коммитом.

## Механизм

1. Автор guard'а пишет шаблон по интуиции или документации, не сверяя с git log реальных коммитов
2. Коммит-скрипт (pre-commit hook, CI, скрипт-pipeline) использует другой синтаксис сообщения
3. Guard возвращает «нет коммита» каждый раз, хотя коммит существует
4. Ложная тревога маскирует реальный статус системы

## Диагностика

```bash
# Шаг 1: посмотреть реальный формат последних коммитов
git log --oneline -10 | grep -i "day-close\|close\|commit"

# Шаг 2: проверить паттерны в guard'е
grep -n "pattern\|grep\|match" scripts/day-open-pipeline.sh

# Шаг 3: запустить self-test guard'а
bash scripts/day-open-pipeline.sh --self-test
```

## Профилактика

1. **Self-test при добавлении guard'а.** Обязательный флаг `--self-test`, проверяющий реальные git log за последние N дней. Если тест не находит ни одного «реального» артефакта за окно — guard сломан.
2. **Keyword-search, не literal-match.** Guard должен искать по ключевому слову (`day-close`) + дата в диапазоне, без привязки к скобкам, двоеточиям и регистру.
3. **Верификация перед деплоем:** `grep реальный_коммит | guard_pattern` → нашёл? Нет → шаблон неверный.

## Связи

- DP.FM.031 (hardcoded-os-path) — аналог для путей файловой системы
- AR.239 (multilingual section validator) — похожий класс literal-pattern guard
- WP-455 — баг-фикс (источник)
