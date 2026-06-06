---
id: DP.M.286
type: method
title: Cold-review pass для frontmatter и cross-anchors после терминологической дизамбигуации
trust: confirmed
epistemic_stage: confirmed
domains: [documentation, pack-curation, cold-context-review]
source_session: 2026-06-05 peer-session 18 WP-349 Ф25b two-axis-consistency
source_commit: cb173d8 (review-01.md DP.ARCH.002)
related: [lessons_subagent_review_for_validators.md]
valid_from: 2026-06-05
schema_version: 1
---

# DP.M.286 — Cold-review pass: frontmatter + cross-anchors

## Контекст

Терминологическая дизамбигуация Pack-документа (полисемичный термин → 2-3 named concept). Автор проходит body-текст и заменяет «X» на «X-в-смысле-A» / «X-в-смысле-B».

## Обнаруженный пропуск (failure-mode для author-only review)

Cold-context review **систематически находит**:

1. **Body-text дизамбигуирован, но frontmatter (`summary`, `tags`, `description`) и Implementation Note / TL;DR / шапочный блок** остались на старом полисемичном термине.
2. **Cross-section anchors** (`см. §N`) промахиваются — после reorganization §N → §M указатели на старый номер.

Причина: автор привык к шапке как «и так знаю что там» → blind spot. Body — фокус внимания при правке. Шапка и cross-refs — на автомате.

## Метод

После любого terminological pass через документ — обязательный cold-context Agent-review с явным prompt:

```
Проверь:
1. frontmatter (yaml-поля: summary, tags, description) — не остался ли старый термин?
2. Implementation Note / TL;DR / summary-блок в шапке — дизамбигуирован?
3. Все cross-references «см. §N» — верифицируй существование и релевантность целевой секции.
```

Cold-context Agent ловит эти позиции быстрее автора: у него нет blind spot «и так знаю».

## Применимо к

- Pack-документы (terminology refactoring)
- ArchGate-документы
- Спецификации (SPF / DP.ARCH / DP.SC)
- Любые документы с многослойной структурой (frontmatter + body + cross-refs)

## Различение

- **lessons_subagent_review_for_validators** — independent review для код-валидаторов / линтеров (catch ошибки в правилах валидации).
- **DP.M.286 (этот метод)** — independent review для content/doc после terminology pass (catch недоделанную дизамбигуацию).

Разные классы артефактов, разные blind spots, одинаковый приём — sub-agent с cold context.

## Связано

- [lessons_subagent_review_for_validators.md](../../../../memory/lessons_subagent_review_for_validators.md)
- WP-349 Ф25b — источник
- DP.ARCH.002 (review-01.md cb173d8) — кейс применения
