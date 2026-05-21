---
id: DP.FM.057
name: cp.iwe в контенте Guides 1-2 — нарушение bounded-context
name_ru: cp.iwe Bounded Context Violation in Guides 1-2
name_en: cp.iwe Bounded Context Violation in Guides 1-2
type: failure-mode
status: active
summary: "Включение cp.iwe (Machine-level competence, ступень 3+) в контент Guides 1-2 (ступени 1-2) создаёт скрытую зависимость: пользователь не может освоить базовый материал без навыков, которых у него ещё нет."
created: 2026-05-20
trust:
  F: 3
  G: domain
  R: 0.85
epistemic_stage: validated
related:
  references: [DP.M.115]
tags: [bounded-context, content-design, cp-indicators, guides, failure-mode]
wp: WP-300
---

# cp.iwe Bounded Context Violation in Guides 1-2 (DP.FM.057)

## 1. Симптом

Подраздел Guides 1 или 2 в frontmatter содержит `cp.iwe` в списке `cp_check`. Линтер выдаёт ошибку (error, не warn).

## 2. Причина

cp.iwe = Machine-level competence (владение IWE-инструментами) — компетенция Guide 3, адресованная ступеням 3+. Guides 1-2 адресованы ступеням 1-2.

Включение cp.iwe в G1-2 создаёт нарушение bounded-context: пользователь Guide 1 («Мировоззрение») не может освоить материал без навыков IWE, которых у него нет.

## 3. Исправление

Убрать `cp.iwe` из `cp_check` в frontmatter подраздела. Допустимо:
- `bh`-метка (поведенческий индикатор)
- Пустой список `cp_check: []`

**Мостовые подразделы (.07) — не исключение:** объяснение принципа усиления ≠ обучение инструменту. cp.iwe запрещён и там.

## 4. Принцип

Каждый guide «не знает» про cp-слоты других guides (bounded context в content design).

Применимо к любой multi-level образовательной системе: каждый уровень аудитории = изолированный bounded context для компетенций.
