---
id: DP.M.141
name: "Выбор source в pack_refs: ID Pack vs docs + ontology_anchor"
type: method
trust: validated
epistemic_stage: confirmed
valid_from: 2026-05-21
domains: [guide-authoring, pack, traceability]
---

# DP.M.141 — Выбор source в pack_refs: ID Pack vs docs + ontology_anchor

## Два режима source в pack_refs

В поле `pack_refs[*]` существует два режима `source` в зависимости от того, прошёл ли концепт полную формализацию:

| Режим | Условие | Пример |
|-------|---------|--------|
| `source: <ID>` | Концепт формализован: существует файл `PACK-*/pack/**/<ID>-*.md` | `source: PD.FORM.127` |
| `source: docs` + `ontology_anchor` | Концепт есть в `ontology.md`, но нет отдельного Pack-файла (status: forming / formalized без выделенной сущности) | `source: docs`, `ontology_anchor: ../../../PACK-personal/ontology.md#профилактика-дрейфа` |

## Алгоритм выбора

```
Есть файл PACK-*/pack/**/<ID>-*.md?
├── Да → source: <ID>
└── Нет, но есть запись в ontology.md?
    ├── Да → source: docs, ontology_anchor: <путь>#<anchor>
    └── Нет → failure mode (концепт не трассируем)
```

## Failure mode: source: docs без ontology_anchor

Указать `source: docs` без `ontology_anchor` → битая трассируемость: anchor недоступен для верификации.

**Тест:** `grep -l "source: docs" *.md | xargs grep -L "ontology_anchor"` → должен быть пустым.

## Применимо к

SS-файлам персональных руководств при добавлении `pack_refs` для концептов, не прошедших полную формализацию как отдельный Pack-файл. Применяется к агентам, пишущим SS-файлы.
