---
id: DP.D.218
name: "regex 'has-data-in-format' ≠ 'success-without-data' in shell output detection"
name_ru: "Regex «есть-данные-в-формате» ≠ «успех-без-данных» — детектор статуса shell-вывода"
type: distinction
domain: digital-platform
pack_refs: []
status: active
valid_from: 2026-06-26
schema_version: 1
source: "session-transcript 2026-06-26, git diff IWE scripts/day-open-preflight.sh d4fe987, WP-445 Ф2"
---

# DP.D.218 Regex «есть данные в формате» ≠ «успех без данных»

## Различение

При парсинге shell-вывода для определения статуса:

| Аспект | Regex «есть данные в формате X» | Явный success-маркер |
|--------|--------------------------------|----------------------|
| **Покрывает** | Успешный ответ С данными | Успешный ответ БЕЗ данных |
| **Пропускает** | Пустой успешный ответ | — (если добавлен) |
| **Риск** | false-fail (пустой успех → ошибка) | false-green (нет) |
| **Пример** | `grep -qE '\| HH:MM \|'` для календаря | `grep -qE '(\| HH:MM \||✅)'` |

## Инвариант

Регулярное выражение, матчащее «формат данных», НЕ матчит случай «инструмент отработал успешно, но вернул 0 результатов».

Результат: корректный пустой ответ квалифицируется как failure (false-fail).

## Два подхода к исправлению

**Подход А: добавить explicit success-marker (если инструмент его выводит)**
```bash
# было:
echo "$OUT" | grep -qE '\| [0-9]{2}:[0-9]{2} \|'
# стало:
echo "$OUT" | grep -qE '(\| [0-9]{2}:[0-9]{2} \||✅)'
```

**Подход Б: инвертировать логику — detect failure, а не success**
```bash
# вместо «нашли данные = ok»
# detect explicit failure pattern:
if echo "$OUT" | grep -qiE 'error|failed|exception'; then
  STATUS=fail
else
  STATUS=ok  # включая пустой успешный ответ
fi
```

## Тест

«Может ли инструмент вернуть пустой успешный ответ, который мой regex квалифицирует как ошибку?»
- Да → добавить explicit success-marker или перейти на detect-failure подход
- Нет (инструмент никогда не возвращает пустой успех) → текущий паттерн допустим

## Связи

- engineering-code-style-base.md P0 (прогони shell-скрипт через shellcheck) — инструментальный уровень
