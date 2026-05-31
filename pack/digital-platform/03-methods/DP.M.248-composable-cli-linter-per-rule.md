---
id: DP.M.248
name: Composable CLI Linter — One Subcommand per Rule
type: method
domain: digital-platform
status: active
valid_from: 2026-05-31
source: DS-principles-curriculum (commits 29a494f, 455813f, 1620e3e, WP-374)
---

# DP.M.248 — Composable CLI Linter: один subcommand на правило

## Суть

Каждое правило domain-specific линтера реализуется как независимый CLI subcommand
(argparse subparser). Добавление правила = новый файл + регистрация subparser.

## Алгоритм

1. Для каждого правила AR.N: создать `check_ar{N}.py` с независимой логикой + error format
2. Зарегистрировать как subparser в `cli.py`: `subparsers.add_parser("ar{N}")`
3. Точка входа: `linter ar3`, `linter ar5`, `linter all`
4. Каждый subcommand: независимый exit code, format, опции

## Пример структуры

```
linter/
  cli.py          # dispatcher + subparsers
  check_ar3.py    # AR.3 логика
  check_ar4.py    # AR.4 логика + tools/data/
  check_ar5.py    # AR.5 логика + glossary
```

## Преимущества

- Selective run: CI может прогонять только изменённые правила
- Независимый error format: AR.3 vs AR.5 разные уровни детализации
- Open/closed: новое правило не трогает существующие файлы
- Testability: каждое правило тестируется изолированно

## Антипаттерн

Монолитный `run_all_checks()` — изменение одного правила требует регрессии по всем.

## Применимость

Любая domain-specific статическая проверка с множеством независимых правил:
- Онтология-линтер (AR.3-AR.5 FPF-чистота)
- Style-checker (независимые стилевые правила)
- Schema-validator (независимые схемы сущностей)

## Связи

- pack_refs: WP-374 (v4-lint AR.3-5 implementation)
- см. также: DP.M.250 (glossary-driven lint) — дополняющий паттерн
