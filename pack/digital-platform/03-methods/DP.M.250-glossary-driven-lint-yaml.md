---
id: DP.M.250
name: Glossary-Driven Lint via YAML — Rules as Data
type: method
domain: digital-platform
status: active
valid_from: 2026-05-31
last_updated: 2026-08-01
source: DS-principles-curriculum tools/data/fpf_entity_type_glossary.yaml (commit 1620e3e, WP-374 AR.5)
---

# DP.M.250 — Glossary-Driven Lint: правила как данные (YAML)

## Суть

Domain-specific правила линтера хранятся в YAML glossary, не в Python-коде.
Domain expert правит YAML — no-code изменение правил без PR.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| No-code поддержка правил vs. выразительность | YAML позволяет domain-эксперту править правила без PR, но не может выразить семантику уровня предложения; метод ограничивает правила substring/context-window, чтобы остаться YAML-driven |
| Точность правила vs. false positives | Строгие aliases и markers ловят ошибки, но флагают легитимные domain-синонимы; метод балансирует точность с полями disambiguation |
| Центральный glossary vs. локальное расхождение | Единый glossary обеспечивает консистентность, но может отставать от доменного использования в отдельных pack; метод принимает центральный source of truth с периодической синхронизацией |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| YAML-completeness fallacy | Практикующий продолжает добавлять поля в glossary, чтобы покрыть каждый edge case, и в итоге выносит в YAML логику, которая должна быть кодом |
| Semantic-overfit bias | Практикующий считает aliases достаточной семантикой и пропускает disambiguation markers, позволяя поверхностным совпадениям терминов заменить контекстуальную оценку |

## Применимость

- Онтологический линтер (FPF entity types)
- Terminology style-checker (согласованность терминологии)
- Schema-validator с domain rules
- Любой domain-specific анализ, где правила часто меняются

## Связи

- pack_refs: WP-374 (v4-lint AR.5, fpf_entity_type_glossary.yaml)
- см. также: DP.M.248 (composable linter architecture) — дополняющий паттерн

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 6). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
