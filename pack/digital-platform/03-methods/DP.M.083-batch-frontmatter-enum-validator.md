---
id: DP.M.083
name: Batch frontmatter enum-validator (pre-commit)
kind: Method
status: active
created: 2026-05-18
sources:
  - WP-300 F4 verification fd430b21 (90 файлов Guide 1 с status=pilot_draft, нет в spec-enum)
  - commit 576a1e3 (batch fix: pilot_draft → draft в 90 файлах)
related:
  complements: [DP.FM.043]   # case-enum-assumption — failure mode без валидатора
  depends_on: []
applies_to:
  - batch-create скелетонов руководств
  - generate из шаблона (≥5 файлов за прогон)
  - migration-скрипты с frontmatter-полями
---

# DP.M.083: Batch frontmatter enum-validator

## Контекст

Скрипт-генератор (batch-skeletons.py и аналоги) создаёт N артефактов одним прогоном. Если автор скрипта поставил placeholder-значение в enum-поле (`status: pilot_draft`, `priority: WIP`, `type: TBD`) и не сверился со спекой — оно тиражируется на все N файлов.

Обнаруживается только: (a) ручной верификацией в cold context (1-2 цикла дрейфа спустя), (b) downstream-инструментом, ожидающим enum (Портной/Аттестатор молча игнорирует или падает).

## Метод

**Источник enum:** спецификация артефакта (YAML schema или explicit list в spec.md).

**Гейт:** pre-commit hook ИЛИ внутренняя проверка в batch-скрипте:

```bash
# Извлечь enum из спеки
ALLOWED=$(grep -oP 'status:\s*\[\K[^\]]+' spec.md | tr ',' '\n')

# Собрать актуальные значения из batch-директории
ACTUAL=$(find <batch-dir> -name "*.md" -exec head -20 {} \; \
  | grep "^status:" | awk '{print $2}' | sort -u)

# Diff — любое расхождение = block
diff <(echo "$ALLOWED" | sort) <(echo "$ACTUAL" | sort) || exit 1
```

## Тест применимости

«Скрипт создаёт >5 артефактов с frontmatter за один прогон?» Да → enum-validator обязателен.

## Контр-pattern

«Поставлю pilot_draft, потом подгоним под spec» — N×(time-to-fix) = drift на ~1 цикл разработки, обнаружение только при cold-audit.

## Связь с другими методами

- DP.FM.043 — failure mode без этого метода (90 файлов pilot_draft).
- feedback_release_gates.md — общий принцип «валидатор без CI = в чужих руках», DP.M.083 = специфический случай для batch ≥5.
- DP.M.011 (Machine-Check Postcondition) — общий шаблон, DP.M.083 — частный для frontmatter enum.
