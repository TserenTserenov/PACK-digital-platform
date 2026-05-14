---
id: DP.FM.015
name: False-Positive Capture Detection (grep vs awk)
kind: FailureMode
status: active
created: 2026-05-08
sources:
  - FMT-exocortex-template commit 8074dfd (2026-05-08)
  - WP-7 Ф-EXTRACTOR-FP
---

# DP.FM.015: False-Positive Capture Detection

## Описание

Экстрактор использует `grep '^### '` для поиска pending-capture-заголовков в captures.md. Grep находит ВСЕ строки с `### ` — включая subsection-заголовки (`### Суть`, `### Релевантность`, `### Применение`) внутри уже помеченных `[analyzed]` блоков.

**Симптом:** счётчик pending = 60 вместо реальных 0-6. Экстрактор запускается впустую или генерирует ложные кандидаты.

## Корень

`grep '^### '` не различает:
- **parent-capture** — самостоятельный блок; в первых 8 строках содержит `**Источник:**` или `**Тип:**`
- **subsection внутри analyzed-capture** — вложенный заголовок; не содержит `**Источник:**` в первых строках

Дополнительно: `\b` (word boundary) не поддерживается в awk — только grep. Использовать awk-совместимые паттерны.

## Решение

Заменить `grep '^### '` на awk, проверяющий первые 8 строк после `### ` на наличие маркеров parent-capture:

```awk
/^### / {
  header = $0; look = 1; lc = 0; next
}
look && /\*\*(Источник|Тип)\*\*/ { print header; look = 0; next }
look { lc++ }
lc >= 8 { look = 0 }
```

Parent-capture = `### ...` + `**Источник:**` или `**Тип:**` в первых 8 строках.

## Связи

- Смежно: P-KE-001 (orphan-fragments — symptom той же структуры)
- Фикс применён: FMT-exocortex-template 2026-05-08
- WP-7 Ф-EXTRACTOR-FP
