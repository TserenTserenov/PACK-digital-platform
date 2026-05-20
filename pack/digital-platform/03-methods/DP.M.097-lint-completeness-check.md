---
id: DP.M.097
name: "Completeness Gate: cross-check spec-множества vs impl-множества для детекции пропущенных случаев"
type: method
status: draft
created: 2026-05-19
valid_from: 2026-05-19
sources:
  - DS-principles-curriculum commit 91faf30 (feat(v4-lint): completeness check — spec vs impl cross-check WP-322 Ф3.11)
related:
  applies_to: []
  references: [DP.M.090]
  complements: []
---

# DP.M.097 — Completeness Gate: spec vs impl cross-check

> Линтер, проверяющий только формат существующих файлов, никогда не найдёт файлы, которых нет.

## Обещание

Обнаруживать «тихие пропуски» — cases/files, которые должны существовать согласно spec, но отсутствуют в impl, не вызывая никаких ошибок валидации.

## Проблема

Типичная lint-система имеет два слоя:
- **Schema-checker:** проверяет формат существующих файлов (frontmatter, структура)
- **Porter/validator:** проверяет, что конкретный файл существует

Но никто не проверяет, что множество существующих файлов СОВПАДАЕТ с множеством объявленных в spec. Пропущенные cases = silent omissions.

## Вход

- Spec-список: перечень ожидаемых artifacts (структура-гайд, конфиг, specification файл)
- Impl-список: реальный набор файлов в целевой директории

## Выход

- `missing[]` — cases в spec, отсутствующие в impl
- Суммарный счётчик для CI gate

## Алгоритм (3 шага)

1. **Собрать spec-множество** — парсить spec-файл (structure-guide, конфиг), извлечь ожидаемые IDs/пути
2. **Собрать impl-множество** — `ls` целевой директории или `find` по паттерну
3. **Diff: spec − impl** — `spec_set - impl_set` → missing cases

## Реализация (bash-шаблон)

```bash
SPEC_SET=$(extract_from_spec "$SPEC_FILE")
IMPL_SET=$(ls "$TARGET_DIR" | normalize_ids)
MISSING=$(comm -23 <(echo "$SPEC_SET" | sort) <(echo "$IMPL_SET" | sort))
if [ -n "$MISSING" ]; then
  echo "MISSING: $MISSING"; exit 1
fi
```

## Применимость

Любой generator/linter с отдельными spec и impl множествами:
- Сборщики руководств с предопределённой структурой разделов
- Pack-линтеры с обязательными файлами
- CI pipelines для структурированных документов
- WP-322 Ф3.11: обнаружено 28 отсутствующих subsections (SS8-SS11 для S6-S10)

## Комплементарность

Не заменяет schema-checker (DP.M.090 — mutation testing) — дополняет как третий слой: формат ✓ + существование ✓ + полнота ✓.
