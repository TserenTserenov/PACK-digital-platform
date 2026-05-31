---
id: DP.M.250
name: Glossary-Driven Lint via YAML — Rules as Data
type: method
domain: digital-platform
status: active
valid_from: 2026-05-31
source: DS-principles-curriculum tools/data/fpf_entity_type_glossary.yaml (commit 1620e3e, WP-374 AR.5)
---

# DP.M.250 — Glossary-Driven Lint: правила как данные (YAML)

## Суть

Domain-specific правила линтера хранятся в YAML glossary, не в Python-коде.
Domain expert правит YAML — no-code изменение правил без PR.

## Структура glossary (YAML)

```yaml
entities:
  система:
    fpf_anchor: "FPF A.2.1"
    aliases: ["система", "целевая система"]
    disambiguation_markers: ["имеет части", "функция"]
    requires_context: ["система обучения", "образовательная система"]
    distinguish_from: ["роль", "мастерство"]
```

## Поля glossary-записи

| Поле | Назначение |
|------|-----------|
| `fpf_anchor` | Ссылка на первоисточник в FPF/SPF |
| `aliases` | Как сущность называется в текстах |
| `disambiguation_markers` | Признаки правильного использования |
| `requires_context` | Признаки смешения/ошибки |
| `distinguish_from` | Частые ложные синонимы |

## Алгоритм lint-функции

1. Загрузить glossary.yaml
2. Для каждой сущности: найти aliases в тексте
3. Проверить наличие disambiguation / requires_context вокруг mention
4. Выдать offset-based сигнал при нарушении

## Граница применимости

- ✅ YAML-driven: подстрочное совпадение + контекстное окно ±N слов
- ❌ YAML-driven: семантическое понимание предложения → LLM-validated

## Применимость

- Онтологический линтер (FPF entity types)
- Terminology style-checker (согласованность терминологии)
- Schema-validator с domain rules
- Любой domain-specific анализ, где правила часто меняются

## Связи

- pack_refs: WP-374 (v4-lint AR.5, fpf_entity_type_glossary.yaml)
- см. также: DP.M.248 (composable linter architecture) — дополняющий паттерн
