---
id: DP.M.239
name: "Defense-in-depth bail-out при refactor regex single→multi: fail-loud вместо silent best-effort"
type: method
status: active
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
valid_from: 2026-05-30
related:
  remediates: [DP.FM.113]
  see_also: [DP.M.153, DP.FM.038]
tags: [refactor, regex, defensive-programming, observability, continuous-deployment]
source: "WP-7 RPA close (peer-session 2026-05-30-31, _split_compute_from_sql:386)"
schema_version: 1
last_updated: 2026-08-01
---

# DP.M.239 — Defense-in-depth bail-out при refactor regex single→multi

## Суть метода

Pattern для безопасного refactor функций обработки regex-match в production коде, когда меняется семантика `search()` (single-match) → `finditer()` (multi-match). Риск: новая функция может встретить вход с несколькими matches, для которого старая логика split/transform была заточена под единственный match — и тихо испортить данные (например, переписать SQL так, что второй call пропал или продублировался).

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Безопасность сохранения старого поведения ↔ прогресс в обработке нового сценария | Bail-out возвращает input без изменений, защищая данные, но откладывает полноценную поддержку multi-match; ранняя реализация multi-match решает кейс, но увеличивает риск регрессии |
| Fail-loud наблюдаемость ↔ шум алертов | CRITICAL-лог + counter гарантируют заметность первого срабатывания, но если boundary case частый, каждый вызов триггерит alert и может вызвать усталость |
| Непрерывное развёртывание ↔ полнота рефакторинга | Bail-out позволяет выкатить фикс быстро, не блокируясь на редком edge-case; но создаёт технический долг, который нужно закрыть по метрикам counter == 0 за N дней |

## Алгоритм

После refactor добавить **bail-out branch** на boundary case (`len(matches) > 1`):

1. **log на уровне CRITICAL/ERROR** с входом (truncated, без PII).
2. **return original input без модификации** (preservation — старая логика для known-safe single-match сохраняется как fallback).
3. **увеличить counter** в metrics для observability (alert при первом срабатывании в проде).

```python
def transform(src: str) -> str:
    matches = list(PATTERN.finditer(src))
    if len(matches) > 1:
        logger.critical(
            "BAIL-OUT: %d matches in transform input, returning unchanged",
            len(matches),
        )
        metrics.increment("transform.bailout")
        return src  # preservation
    if not matches:
        return src
    # original single-match logic
    return apply_transform(src, matches[0])
```

## Принципы

| Принцип | Что значит |
|---------|-----------|
| **fail-loud** | CRITICAL log + counter; не silent best-effort |
| **preserve-semantics** | вернуть до-refactor поведение для known-safe single-match case'а |
| **observable** | alert при первом срабатывании в проде |

## Антипаттерны

| Антипаттерн | Что плохо |
|-------------|-----------|
| Бросить exception | крашит rule loading → blocks **all** rules, а не только проблемную |
| Молча обработать первый и игнорировать остальные | silent data loss, регрессия незаметна |
| Удалить bail-out после первого «зелёного» прогона | возвращает риск без свидетельств безопасности |

## Тест применимости

«Функция меняет данные на основе regex match, и refactor расширяет match-кардинальность?»
- **Да** → требуется bail-out на boundary до закрытия issue по полному multi-match handling.
- **Нет** (refactor сохраняет cardinality, или функция read-only) → bail-out не нужен.

## Связи

- **Remediates DP.FM.113** — `re.search()` глотает второе нарушение в multi-violation validators.
- **Continuous-deployment контекст:** bail-out = «временный foothold», позволяющий выкатить fix критической ошибки, не блокируясь на полной поддержке нового сценария. После накопления свидетельств безопасности (метрики counter == 0 за N дней) → bail-out можно снять или превратить в стандартную обработку multi-match case.

## Применимо к

SQL transformers, code-mod tools, AST rewriters, parser refactor, любые `search → find_all` миграции в обработчиках.

## Прецедент

WP-7 RPA close (2026-05-30): `_COMPUTE_CALL_RE.search(sql)` → `finditer(sql)` с safety bail-out при `len > 1` в `_split_compute_from_sql:386`. Bail-out поймал production-input с двумя SQL-вызовами, что было бы переписано неверно через старую single-match логику.
