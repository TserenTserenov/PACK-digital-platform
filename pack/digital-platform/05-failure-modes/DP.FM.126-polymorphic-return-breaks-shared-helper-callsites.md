---
id: DP.FM.126
name: "Полиморфный return type на shared helper ломает downstream callsites молча"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: refactoring
severity: major
valid_from: 2026-05-30
related:
  see_also: []
tags: [refactoring, contract-drift, type-system, shared-helper, silent-bug, python]
source: "session 2026-05-30 WP-330 (peer-session 29, 01-peer.md, report-draft.md §2 Тема 1 КС2-1)"
schema_version: 1
---

# DP.FM.126 — Полиморфный return type на shared helper ломает downstream callsites молча

## Описание

Failure mode при расширении функциональности shared helper'а: новый case требует возврата другого типа → автор расширяет return type (`str | None` → `str | dict | None`). На новом callsite всё работает. **Downstream callsites, использующие старый контракт, ломаются молча.**

В Python без runtime type-check и без full type-coverage расхождение не ловится:
- `if not text:` для dict даёт `False` (truthy) — guard срабатывает наоборот.
- `f"{text}: ..."` для dict форматирует `{key: value}` как repr — внешне «строка», но не та.
- Логирование тоже работает (str(dict) — валидный str), что заглушает diagnostic.

## Симптом

- На новом callsite — работает.
- На старом callsite — pipeline продолжает работать, но артефакты выглядят странно (digest содержит `{key: value}` вместо текста, weekly summary — мусор).
- Reviewer пропускает PR: новый сценарий протестирован, существующие тесты проходят (они не покрывают новый return-variant на старом callsite).

## Механизм

1. Shared helper `build_message(ctx) -> str | None` — есть >1 callsite.
2. Новая фича требует возврата структуры (dict с extra fields).
3. Author расширяет: `def build_message(ctx) -> str | dict | None`.
4. Новый callsite branch'ится на type: `if isinstance(r, dict): handle_new(r) else: handle_str(r)`.
5. Старые callsites — без isinstance-guard. При новом context'е получают dict.
6. `if not result:` для dict — False → проходят в branch «success».
7. Дальше dict идёт в f-string / log / БД-write — silent corruption.

## Canonical fix

Branch на callsite **до** вызова shared helper, не внутри. Helper остаётся с узким контрактом:

```python
# антипаттерн
def build_message(ctx): return dict_form if ctx.kind == 'X' else str_form

# canonical
def build_str_message(ctx) -> str | None: ...
def build_dict_message(ctx) -> dict | None: ...

# callsite
if needs_dict: r = build_dict_message(ctx)
else: r = build_str_message(ctx)
```

Если новая логика сложная — отдельная функция с собственным контрактом, не overload существующей.

## Тест применимости

«Функция вызывается из >1 места, и я добавляю новый return-variant?» — да → branch на callsite, не в функции.

## Antipattern-сигнал

PR с заголовком «Add support for X in build_*» и diff'ом, расширяющим Union return type → red flag, искать другие callsites через grep.

## Diagnostic

Code review: на shared helpers (любая функция с ≥2 callsites) — обязательная проверка downstream при изменении return-type signature. CI lint: запрет Union return на функциях в `utils/`, `helpers/`, `shared/`.

## Применимо к

format/serialize/build helpers, util-функции с >1 callsite, любые «utility» функции в shared modules. Python (без runtime types) — high risk. Go/Rust c interface{}/enum padding shared functions — аналогичный риск.