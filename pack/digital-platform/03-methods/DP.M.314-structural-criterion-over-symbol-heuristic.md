---
id: DP.M.314
name: "Structural criterion over symbol heuristic"
name_ru: "Структурный критерий вместо символьной эвристики в markdown-валидаторах"
name_en: "Structural criterion over symbol heuristic for markdown artifact validators"
summary: "При дизайне lint/audit-скриптов для markdown-артефактов проверять AST-структуру (заголовки уровней + непустые блоки), а не символьные паттерны (пунктуация →, :, *). Символьные эвристики дают false-positive на заголовках и false-negative на альтернативных нотациях."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: lint-design
valid_from: 2026-06-17
related:
  see_also: [DP.M.313, DP.FM.148]
tags: [lint, validator, markdown, structural-criterion, audit-script, heuristic, false-positive]
source: "WP-422 Ф8, peer-session 2026-06-17, разбор критерия content-audit.sh"
schema_version: 1
---

# DP.M.314 — Структурный критерий вместо символьной эвристики в markdown-валидаторах

## Описание

При дизайне скриптов проверки markdown-артефактов (скиллы, методы, ADR, RP-карточки) используй структурный критерий вместо символьной эвристики. Символьная эвристика проверяет наличие определённых символов или пунктуации; структурный критерий проверяет наличие заголовков нужного уровня с непустым содержанием после них.

## Algorithm

### Step 1: Определи, что реально обязательно

Сформулируй критерий через структуру документа:
- «Секция `## Algorithm` содержит ≥1 подраздела `### Step N` с ≥M строк текста после заголовка»
- Не через: «в документе есть строка с символом `→` или `:`»

### Step 2: Реализуй структурный матч

Для markdown:
```python
# Структурный критерий (правильно)
def has_algorithm_steps(text):
    sections = re.findall(r'### Step \d+.*?\n(.*?)(?=### |## |$)', text, re.DOTALL)
    return sum(1 for s in sections if len(s.strip()) >= MIN_CONTENT_LINES) >= MIN_STEPS

# Символьная эвристика (хрупко)
def has_algorithm_heuristic(text):
    return bool(re.search(r'[→:]', text))  # false-positive на заголовках
```

### Step 3: Применяй тест применимости

Задай вопрос: «Может ли заголовок секции случайно пройти эвристику?»
- Да → переходи к структурному критерию
- Нет → символьная эвристика допустима для данного случая

## When to use

- При написании CI-скрипта проверки структуры markdown-артефактов (skill, method, ADR)
- При рефакторинге существующего heuristic-детектора с высоким false-positive rate
- При дизайне DoD-критерия фазы, который будет проверяться автоматически

## Тест применимости

«Может ли заголовок секции `### Step 1:` случайно пройти критерий?»
- Для символьной эвристики на `:` — да, это false-positive
- Для структурного критерия — нет, заголовок без содержания не считается

## Связи

- DP.M.313 — enforcement ladder; структурный критерий = вход для E2-promotion (однозначная автоматическая проверка)
- DP.FM.148 — regex-детектор с семантической слепотой (антипаттерн неудачного E2)
- DP.D.147 — минимальный барьер ≠ полное качество; данный метод улучшает точность минимального барьера
