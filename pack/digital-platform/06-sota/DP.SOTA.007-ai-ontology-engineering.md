---
id: DP.SOTA.007
name: AI-Accelerated Ontology Engineering
type: sota
status: active
summary: "LLM ускоряют онтологическую инженерию в 10x: моделирование, расширение, population, alignment, entity disambiguation"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 3
  G: external
  R: 0.6
related:
  integrates_with: [DP.SOTA.001, DP.SOTA.004, DP.SOTA.008]
  enables: [DP.M.001]
  operationalized_by: [DP.AISYS.013]
tags: [ontology, knowledge-engineering, llm, automation, extraction]
---

# AI-Accelerated Ontology Engineering

## 1. Определение

**AI-Accelerated Ontology Engineering** — использование LLM для ускорения традиционно ручных задач онтологической инженерии: моделирование (создание структуры), расширение (добавление новых концепций), population (наполнение экземплярами), alignment (согласование между онтологиями), entity disambiguation (устранение неоднозначностей).

## 2. Статус SOTA (февраль 2026)

**SOTA, breakthrough phase.**

- **LLM решают историческое узкое место** — ручная онтологическая работа, которая требовала экспертов и месяцев, теперь выполняется за часы с LLM-ассистом.
- **LOT4KG methodology** (2025) — расширяет традиционную онтологическую инженерию для включения lifecycle management knowledge graphs.
- **Онтологии из структурированных данных** дешевле и не уступают по качеству онтологиям из текстовых корпусов.

## 3. SPF-интеграция

| SPF-механизм | AI Ontology аспект |
|-------------|-------------------|
| Pack manifest (Entity Index) | Автоматически генерируемый реестр = ontology index |
| Typed `related:` | Типизированные связи = ontology relations |
| ontology.md | Иерархия типов + глоссарий = domain ontology |
| Knowledge Extraction (DP.M.001) | LLM-assisted extraction = AI-accelerated OE |

## 4. Реализация в экзокортексе

Наш Knowledge Extractor (DP.AISYS.013) — это реализация AI-Accelerated OE:
- **detect** = ontology gap detection
- **classify** = concept classification
- **route** = ontology placement
- **formalize** = concept formalization with frontmatter
- **validate** = consistency check

## 5. Правила применения

1. **При экстракции знаний** — использовать LLM для первого прохода (draft classification), человек валидирует.
2. **При создании ontology.md** — генерировать из frontmatter автоматически, не писать вручную.
3. **При росте Pack** — периодически запускать Knowledge Audit (alignment между сущностями).
4. **Не доверять LLM на 100%** — валидация через F-G-R trust model обязательна.

## 6. Источники

- ScienceDirect. «Accelerating KG/Ontology Engineering with LLMs» (2025)
- LOT4KG methodology. ACM, ISWC 2025
- Anthropic internal practices: Knowledge Extraction patterns
