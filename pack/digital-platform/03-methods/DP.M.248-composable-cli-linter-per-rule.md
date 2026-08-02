---
id: DP.M.248
name: Composable CLI Linter — One Subcommand per Rule
type: method
domain: digital-platform
status: active
valid_from: 2026-05-31
last_updated: 2026-08-01
source: DS-principles-curriculum (commits 29a494f, 455813f, 1620e3e, WP-374)
---

# DP.M.248 — Composable CLI Linter: один subcommand на правило

## Суть

Каждое правило domain-specific линтера реализуется как независимый CLI subcommand
(argparse subparser). Добавление правила = новый файл + регистрация subparser.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость selective run vs. consistency всего suite | Запуск только изменённых правил быстр, но рискует пропустить cross-rule взаимодействия; метод изолирует правила, чтобы CI мог выбирать, но каждое правило остаётся независимо тестируемым |
| Open/closed extensibility vs. discoverability | Добавление правил через новые файлы не трогает существующую логику, но размывает поверхность линтера; метод принимает дисперсию ради модульности |
| Специфичный формат ошибки vs. uniform tooling | Каждое правило может требовать свой output schema; метод позволяет каждому subcommand определять формат вместо навязывания общего, который ослабляет диагностику |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Convenience-driven consolidation | Практикующий видит повторяющийся `subparsers.add_parser` как boilerplate и сворачивает правила в монолитный dispatcher, возвращая регрессионную поверхность, которую метод исключает |
| Rule-isolation fundamentalism | Практикующий разбивает каждый тривиальный вариант на отдельный subcommand, фрагментируя CLI и делая набор правил сложнее для обнаружения, чем предполагает метод |

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

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 6). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
