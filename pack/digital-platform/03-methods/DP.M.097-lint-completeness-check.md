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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Детекция отсутствующего ↔ цена поддержки spec-источника | Diff `spec − impl` ловит silent omissions (28 отсутствующих subsections в WP-322 Ф3.11), но требует актуального spec-файла — устаревший spec превращает gate в генератор ложных `missing[]` |
| Узкая механика set-difference ↔ широта проверки | `comm -23` тривиален и детерминирован, но находит только объявленное в spec; метод сознательно не проверяет лишние файлы и смысловую корректность, оставляя их schema-checker'у и DP.M.090 как отдельные слои |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Epistemic stage в frontmatter не указан._

| Bias | Direction of distortion |
|------|--------------------------|
| Проверка существующего затмевает отсутствующее | Внимание инвестируется в schema-проверки формата файлов, которые есть — их ошибки видны и красят CI, — а множество `spec − impl` никто не считает, потому что «тихий пропуск» не шумит: в WP-322 Ф3.11 так накопилось 28 отсутствующих subsections |
| Заглушки ради зелёного CI вместо разбора missing | При срабатывании gate внимание съезжает к быстрому созданию недостающих файлов-пустышек, лишь бы счётчик обнулился, вместо проверки, должны ли эти cases вообще существовать по spec или spec устарел |

## Комплементарность

Не заменяет schema-checker (DP.M.090 — mutation testing) — дополняет как третий слой: формат ✓ + существование ✓ + полнота ✓.

---

> 2026-07-31 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 4). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
