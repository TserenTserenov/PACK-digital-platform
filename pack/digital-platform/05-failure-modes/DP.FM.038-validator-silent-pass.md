---
id: DP.FM.038
name: Silent-Pass Validator on Missing Input (Валидатор зеленеет на отсутствующем входе)
category: tooling
severity: major
status: draft
summary: "Валидатор на отсутствующем или пустом входе возвращает exit 0 (нечего нарушать), создавая false-green в CI/pre-commit. Опечатка в пути или несостоявшийся checkout → нулевая проверка → ложно-положительный сигнал."
created: 2026-05-16
valid_from: 2026-05-16
related:
  extends: [DP.FM.030]
  see_also: [DP.FM.032]
tags: [validator, ci, false-positive, fail-loud, tooling]
source: "WP-321 Ф9 B1 — DS-principles-curriculum commit 0071e0b: v4-lint structure /tmp/nonexistent → exit 0"
schema_version: 1
---

# [DP.FM.038] Silent-Pass Validator on Missing Input

## Суть паттерна

Валидатор на пустом или несуществующем входе возвращает exit 0 («ничего не нарушено»). В CI это выглядит как зелёный билд. Опечатка в пути, несостоявшаяся checkout-команда или удалённая директория → нулевая проверка → ложно-положительный сигнал.

Обобщение HD #51 («HTTP 200 + 0 bytes = false-green») от HTTP-ответов на любой validator output.

## Механизм

1. Валидатор принимает путь как аргумент (`v4-lint structure $PATH`).
2. На входе `$PATH = /tmp/nonexistent` (опечатка) или пустая директория после неудачного checkout.
3. Внутренний loop по файлам не находит ничего → 0 нарушений → exit 0.
4. CI green — но проверки не было.

## Защита

Валидатор обязан:

1. **Явно проверять существование входа** — `[ -e "$path" ]` → exit 1 с сообщением «путь не существует».
2. **Явно проверять non-empty корпус файлов** — если `len(files) == 0` → exit 1 с сообщением «не найдено файлов для проверки».
3. **Возвращать ненулевой код в обоих случаях** + явное сообщение в stderr.

```python
def validate(path):
    if not os.path.exists(path):
        sys.exit(f"ERROR: путь не существует: {path}")
    files = collect_structure_files(path)
    if not files:
        sys.exit(f"ERROR: не найдено файлов для проверки в {path}")
    # ... обычная валидация
```

## Применимость

Любой tool с pipeline-интеграцией: pre-commit hooks, CI gates, scheduled audits, lint-инструменты, schema-validators.

## Связь с другими FM

- **HD #51** (HTTP 200 + 0 bytes = false-green) — частный случай для HTTP-ответов.
- **DP.FM.030** (Compliance Matrix Narrative Drift) — родственный паттерн «зелёного результата при отсутствии содержательной проверки».
- **DP.FM.039** (Silent-Null Parser on Unknown Syntax) — silent-fail на уровне парсера, ниже валидатора в pipeline.
