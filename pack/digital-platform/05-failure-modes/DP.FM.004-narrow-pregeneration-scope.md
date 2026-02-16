---
id: DP.FM.004
type: failure-mode
status: draft
created: 2026-02-16
trust:
  F: 3
  G: domain
  R: 0.8
epistemic_stage: validated
parent_concept: U.Performance
related:
  - DP.SOTA.002
  - DP.AISYS.014
---

# FM: Narrow Pre-Generation Scope

## 1. Паттерн ошибки

Система pre-generation оптимизирует только для следующего атомарного шага пользователя, игнорируя полный сценарий взаимодействия в рамках одной сессии.

**Симптомы:**
- Первый шаг сессии быстрый (pre-generated)
- Последующие шаги в той же сессии медленные (on-the-fly generation)
- Bottleneck возникает не в начале, а в середине user flow

**Пример (Marathon bot):**
- Scheduler pre-generates только для следующего доступного топика
- Пользователь в одной сессии проходит теорию + практику (два топика одного дня)
- Практика = 11.6s Claude API call (не было pre-generated)

## 2. Антипаттерн → Паттерн

| Антипаттерн | Паттерн: Lookahead Pre-Generation |
|-------------|-----------------------------------|
| Pre-generate для next atomic step | Pre-generate для **session scope** (все шаги до естественного break) |
| Scope = «что нужно сразу» | Scope = «что пользователь сделает за один заход» |

**Алгоритм:**
1. Определить session boundary — где пользователь обычно делает паузу
2. Pre-generate весь контент до этой границы
3. Балансировать: compute cost vs UX cost (latency в сессии)

## 3. Тест обнаружения

**Вопрос:** Pre-generation покрывает только следующий шаг или все действия до естественного break?

**Метрика:** Latency distribution по шагам в user flow. Если step 1 <1s, а step 2+ >5s — narrow scope.
