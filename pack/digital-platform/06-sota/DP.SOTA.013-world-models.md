---
id: DP.SOTA.013
name: World Models
type: sota
status: active
summary: "Переход от LLM (модели знаний о мире) к World Models (модели мира): замыкание цикла действие-измерение-обновление, три линии исследований, архитектурные импликации для AI-агентов"
created: 2026-02-19
edition: "2026-02"
source: "Левенчук, Как поумнеть человеку или роботу, 19 фев 2026"
trust:
  F: 4
  G: external
  R: 0.7
related:
  extends: [DP.SOTA.006]
  integrates_with: [DP.SOTA.002, DP.SOTA.009]
  operationalized_by: [DP.M.007]
  distinguishes: [DP.D.029]
  prevents: [DP.FM.005, DP.FM.003]
tags: [world-model, llm, embodied-intelligence, open-endedness, lecun, schmidhuber]
---

# World Models

## 1. Определение

**World Model** — модель, которая замыкает цикл действие → измерение → обновление: хранит состояние мира, прогнозирует последствия действий и превращает невязку (расхождение прогноза и реальности) в обновление собственных представлений.

В отличие от LLM («модели знаний о мире из текстов»), world model получает знания из непосредственного взаимодействия с миром, а не из описаний мира в книгах.

## 2. Три значения термина

| # | Вариант | Суть | Линия |
|---|---------|------|-------|
| 1 | **Model-based RL** | Обучаемый симулятор динамики: «воображает» будущие траектории, выбирает действия по imagined rollouts | Ha & Schmidhuber 2018 → MuZero, Dreamer |
| 2 | **Latent predictive** | Предсказание в абстрактном пространстве представлений без генерации пикселей/текстов; семантические, пригодные для контроля и планирования представления | LeCun JEPA (I-JEPA, V-JEPA) |
| 3 | **Not injection** | RAG, retrieval, инструменты и дообучение на текстах расширяют «кабинетное знание», но не заменяют модель, обновляемую из взаимодействия | arxiv 2602.01630 |

Все три варианта ведут к одному: AI-агенту, который замыкает цикл действие–измерение–обновление устойчиво при масштабировании.

## 3. Статус SOTA (февраль 2026)

**Emerging, pre-mainstream.** LLM-based агенты доминируют, но осознание ограничений растёт:

- LeCun (Meta FAIR): JEPA-архитектура как альтернатива генеративным моделям
- «Research on World Models Is Not Merely Injecting World Knowledge into Specific Tasks» (arxiv 2602.01630): нормативный фреймворк = interaction + perception + symbolic reasoning + spatial representation
- «From Kepler to Newton» (arxiv 2602.06923): три минимальных inductive bias превращают трансформер из curve-fitter в «физика»
- Helix architecture (arxiv 2401.15185): multi-rate layered control для embodied intelligence

## 4. SPF-интеграция

| SPF-слот | Что даёт |
|----------|----------|
| Различение (D.029) | LLM ≠ World Model — чёткий критерий для архитектурных решений |
| Failure Mode (FM.005) | Model-Reality Drift — что происходит без measurement loop |
| Метод (M.007) | Intervention Loop — как замкнуть цикл на платформе |
| Harness (D.025) | Harness = inductive bias, компенсирующий ограничения LLM |

## 5. Импликации для платформы

Экзокортекс сегодня = AI-агенты на LLM = «кабинетные учёные». Компенсация:

1. **Human-in-the-loop** как «прокладка» между LLM и реальным миром (DP.SOTA.006 правило 4)
2. **Capture-to-Pack** = измерение на рубежах, не генерация «из головы»
3. **Intervention Loop** (DP.M.007) = формализованный цикл проверки модели реальностью
4. **Unsatisfied-questions** + обратная связь пользователей = сигнал невязки

Не пытаться превратить LLM в world model. Вместо этого — строить harness, который компенсирует отсутствие measurement loop.

## 6. Источники

- Ha D., Schmidhuber J. «World Models» (2018). arxiv 1803.10122
- Hafner D. et al. «Dream to Control: Learning Behaviors by Latent Imagination» (Dreamer, 2019). arxiv 1912.01603
- Schrittwieser J. et al. «Mastering Atari, Go, Chess and Shogi by Planning with a Learned Model» (MuZero, 2019). arxiv 1911.08265
- Assran M. et al. «Self-Supervised Learning from Images with a Joint-Embedding Predictive Architecture» (I-JEPA, 2023). arxiv 2301.08243
- Bardes A. et al. «V-JEPA: Latent Video Prediction for Visual Representation Learning» (2024). arxiv 2404.08471
- «Research on World Models Is Not Merely Injecting World Knowledge into Specific Tasks» (2026). arxiv 2602.01630
- «From Kepler to Newton: Inductive Biases Guide Learned World Models in Transformers» (2026). arxiv 2602.06923
- «Towards a Theory of Control Architecture» (Helix, 2024). arxiv 2401.15185
- Chollet F. «On the Measure of Intelligence» (2019). arxiv 1911.01547
- Левенчук А. «Как поумнеть человеку или роботу» (2026-02-19). ailev.livejournal.com/1791964
