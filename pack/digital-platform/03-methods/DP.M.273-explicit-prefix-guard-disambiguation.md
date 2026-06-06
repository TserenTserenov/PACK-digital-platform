---
id: DP.M.273
title: "Explicit-prefix guard для disambiguation двух путей к одной фиче"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-04
source: session-transcript 2026-06-04-50 (WP-392 Ф3.1 bug-prefix-fix), git diff aist-bot 800a112 (fallback.py prefix guard)
related:
  see_also: [DP.M.077, DP.METHOD.030]
---

# DP.M.273 — Explicit-prefix guard для disambiguation

## Описание

Когда к одной фиче (новый агент, инструмент, режим) ведут два пути — UI-сессия и текстовый fallback в routing — возникает риск непредсказуемости: одинаковый текст пользователя → разное поведение в зависимости от состояния FSM.

**Паттерн:** текстовый путь активируется только при явном prefix-маркере (`startswith("PrefixName")`, регистронезависимо, strip punctuation). Без prefix → тихий fallback в основной routing, новая фича не вызывается.

## Принцип

**Пользователь сам выражает intent.** Без prefix — поведение детерминировано (старый routing). С prefix — детерминированно вызывается новая фича. Никакого «угадывания intent через LLM-классификатор» на каждом сообщении.

## Эффекты

1. **Предсказуемость.** Одинаковый текст без prefix всегда даёт одинаковый routing.
2. **Совместимость с системой ролей.** Дискриминация совпадает с уже существующим паттерном префиксов ролей (`«Навигатор, …»`, `«Диагност, …»` из `role-prefixes.md`).
3. **MVP-уровень.** Без discoverability-хинта (можно добавить позже как backlog-overlay).

## Алгоритм применения

1. Идентифицировать фичу с двумя путями: UI-сессия + текстовый fallback.
2. Определить prefix-маркер (имя сущности, регистронезависимо, с пунктуацией).
3. В fallback-handler: `if text.lower().lstrip(string.punctuation).startswith(prefix): → новая фича else: → старый routing`.
4. Документировать prefix в публичной справке (или принять как known limitation MVP без хинта).

## Тест

«Может ли пользователь случайно попасть в новый канал, не зная о нём?» Да → нужен prefix-guard.

## Антипаттерн

Автоопределение intent через LLM-классификатор на каждом сообщении → overhead + ложные срабатывания + потеря детерминизма.

## Связи

- `role-prefixes.md` (existing convention) — каноничный пример: `«Навигатор, …»`, `«Диагност, …»`, `«Создатель паков, …»`, `«Оргразвитие, …»`.
- DP.M.077: common-prefix-compression (другая семантика prefix — компрессия, не routing).
- DP.METHOD.030: term-translation (расширение: prefix как маркер контекста).
