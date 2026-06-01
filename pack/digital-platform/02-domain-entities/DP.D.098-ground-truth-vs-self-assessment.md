---
id: DP.D.098
name: "Ground truth ≠ Self-assessment для валидации proxy-моделей"
type: distinction
kind: Distinction
pack: PACK-digital-platform
domain: validation
trust: 0.85
epistemic_stage: validated
status: active
valid_from: 2026-05-25
sources:
  - "session-close-feed 2026-05-25 (WP-353 Ф1 Kimi peer-review commit c47f96be — cognitive-proxy-pipeline.py)"
---

# DP.D.098 — Ground truth ≠ Self-assessment для валидации proxy-моделей

> При валидации proxy-модели (LLM выводит характеристику из текста пилота) использование self-assessment как ground truth создаёт **circularity bias**: proxy и truth исходят из одного источника (текст пилота), их корреляция артефактна и не доказывает работоспособность модели. Ground truth должен приходить из независимого канала.

## Различение

| Аспект | (а) Self-assessment как ground truth | (б) Independent ground truth |
|--------|--------------------------------------|------------------------------|
| **Источник truth** | Пилот сам сообщает свою характеристику | Formal assessment / expert rating / behavioral metric |
| **Источник proxy** | Текст того же пилота | Текст того же пилота |
| **Корреляция proxy↔truth** | Артефактна (общий источник смещения) | Содержательна (если есть) |
| **Что доказывает high correlation** | Proxy умеет читать декларацию пилота | Proxy умеет предсказать независимо измеренное свойство |
| **Применимость для решений** | Только калибровка декларации | Принятие решений о пилоте |

## Антипаттерн (circularity bias)

LLM анализирует текст пилота → выводит «уровень мастерства = 3.5». Сравниваем с self-report того же пилота «я считаю себя на ступени 3» → корреляция 0.85. Объявляем proxy валидной.

**Проблема:** пилот в обоих случаях писал из одного состояния, с одним набором мемов, в одном style. Proxy с высокой вероятностью улавливает не объективное мастерство, а **стиль самоописания** пилота. Использование такой proxy для решений (что показать в УИ, какой контент рекомендовать) усилит уже существующее самовосприятие, не скорректирует его.

## Правильный паттерн (independent ground truth)

Источники независимого ground truth:
- **Formal assessment** — структурированный диагностический тест с эталоном оценки (например, `learning.cp_assessments`).
- **Expert rating** — третья сторона (наставник, peer) оценивает по той же рубрике.
- **Behavioral metric** — наблюдаемое поведение в системе (commits, completed tasks, response latency на типовые задачи).
- **Outcome metric** — результат пилота через N времени (стартап выжил, статья опубликована).

Корреляция proxy с любым из них = содержательный сигнал.

## Тест

Перед валидацией proxy-модели ответить:
1. Из какого канала приходит proxy-input? (обычно: текст / поведение пилота)
2. Из какого канала приходит ground truth?
3. **Совпадают ли каналы?** Да → circularity, переформулировать валидацию. Нет → независимый сигнал.

## Применимость

- Валидация LLM-классификаторов человеческих характеристик (уровень мастерства, настроение, готовность).
- A/B-тесты с self-reported метриками (CSAT, NPS) — независимый measurement обязателен для каузальных выводов.
- Recommender systems — clicks ≠ value (clicks могут быть proxy для curiosity, не satisfaction).

## Связи

- **Применяется в:** WP-353 Ф1 cognitive-proxy-pipeline — ground truth = `learning.cp_assessments` от Диагноста, не self-report.
- **Соблюдает:** общий принцип измерения с independent measurement (psychometrics, RCT methodology).
- **Соседствует:** DP.D.094 (Temporal correlation ≠ Causation) — circularity = другой класс false-positive в proxy-validation.
