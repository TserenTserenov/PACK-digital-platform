---
id: DP.M.077
name: Common-prefix compression в output путей и циклов
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-322 Ф1.3 (DS-principles-curriculum/890b15b — v4-lint.py _format_cycle)
  - session-transcript 2026-05-17
related:
  applies_to: [linter, cycle-detection, dependency-resolver, ontology-prerequisites, import-cycle errors]
---

# DP.M.063: Common-prefix compression в output путей и циклов

## Определение

Метод форматирования вывода линтеров/анализаторов, эмитящих путь/цикл по графу: вычислить общий префикс всех узлов в пути, вынести в конец как metadata, показать только различающиеся хвосты узлов. Компактно, без потери информации.

## Проблема

Наивный вывод полного пути с FQ-именами быстро становится нечитаемым:

```
PD.GUIDE.1.S1.SS2 → PD.GUIDE.1.S1.SS3 → PD.GUIDE.1.S1.SS2
```

Глаз ищет различающиеся части, тонущие в повторах.

## Триггер применимости

> «Выводит ли инструмент путь длиной ≥2 узлов с FQN-именами, повторяющими общий префикс?»

- **Да** → common-prefix compression.
- **Нет** (короткие имена или одноуровневые) → не нужно.

## Алгоритм

1. **Reduce common prefix** через все path-nodes.
2. **Display tails-only** как путь (`SS2 → SS3 → SS2`).
3. **Append suffix** `(length=N, scope=<prefix>)` с metadata: длина пути и общий контекст.

## Пример

**До:** `PD.GUIDE.1.S1.SS2 → PD.GUIDE.1.S1.SS3 → PD.GUIDE.1.S1.SS2`
**После:** `SS2 → SS3 → SS2 (length 2, in PD.GUIDE.1.S1)`

## Применимость

- Cycle-detection output (graph-algorithms).
- Import-cycle errors (Python, JS module bundlers).
- Ontology-prerequisites circles.
- Dependency-resolver output (yarn, pip, cargo).
- Любой tool, который пишет path/cycle с длинными FQN-именами.

## Связи

- **Дополняет** общие best practices форматирования lint-output (`filename:line:col + сообщение`).
