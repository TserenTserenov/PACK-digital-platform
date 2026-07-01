---
id: DP.METHOD.098
type: method
domain: PACK-digital-platform
status: draft
summary: "Выделенный пост-генерационный gate с детерминированным словарём проверяет стиль/compliance LLM-вывода. Style-инструкция в промпте = 'попросить'; gate = 'проверить'. Оба нужны."
created: 2026-07-01
trust:
  F: 3
  G: domain
  R: 0.87
epistemic_stage: evidence
related:
  see_also: [DP.M.066, DP.SC.050]
tags: [pipeline, style-compliance, verification, llm-output, gate-pattern, deterministic]
wp: WP-149
---

# Выделенный стиль-гейт в pipeline (DP.METHOD.098)

## Описание

В pipeline с LLM-генерацией добавить отдельный детерминированный gate, который проверяет стиль/compliance вывода после генерации, до доставки.

## Проблема

Style-инструкция в generation prompt («не используй жаргон», «только русский язык») недостаточна:
- LLM игнорирует инструкцию при высокой частотности запрещённых слов в данных (anchoring)
- EN-слова из переменных промпта проникают в output, несмотря на запрет

## Паттерн

```
[scaffold] → [llm-generation] → [style-gate] → [delivery]
                                     ↑
                         детерминированный словарь
                         + YELLOW / RED при нарушении
```

**Gate содержит:**
- Словарь запрещённых слов/паттернов
- Уровни: YELLOW (предупреждение, логирование) или RED (блокировка доставки)
- Полная детерминированность (нет LLM внутри gate)

## Инвариант

Style-инструкция в промпте + gate = синергия двух слоёв:
- Без gate инструкция ненадёжна (LLM «забывает»)
- Без инструкции gate даёт шум (LLM не понимает, что именно хочет промпт)

## Применение

- Pilot-facing артефакты (персональные руководства, дневные планы)
- Любой pipeline с требованиями к стилю (tone-of-voice, язык, security-patterns, PII)

## Отличие от LLM-as-judge (DP.M.066)

- DP.M.066: LLM оценивает семантику и качество
- DP.METHOD.098: детерминированный словарь проверяет лексику/синтаксис

Оба применимы совместно.

## Связи

- DP.M.066 — multi-round verifier (семантический уровень)
- DP.SC.050 — style contract для pilot-facing артефактов
