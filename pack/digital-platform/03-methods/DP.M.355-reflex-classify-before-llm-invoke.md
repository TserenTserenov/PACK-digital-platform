---
id: DP.M.355
name: "Reflex classify-before-llm-invoke dispatcher gate"
name_ru: "Рефлекс-routing: classify() обязан предшествовать invoke_claude()"
name_en: "Reflex classify-before-llm-invoke: dispatcher gate invariant"
summary: "В двухконтурном диспетчере (рефлекс + LLM) classify() по сигнатуре признаков должна вызываться ДО invoke_claude(). Нарушение: рефлекс-маршрут не экономит токены и не снижает latency. Дополнительный инвариант: confidence=0.0 для LLM-пути (неопределённая уверенность)."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: dispatcher-architecture
valid_from: 2026-06-20
related:
  see_also: [DP.M.170, DP.M.112, DP.D.075]
tags: [dispatcher, reflex-routing, classify, invoke-claude, two-circuit, confidence, latency]
source: "WP-295 Ф3-core, commit 404a4f543 (DS-my-strategy: iwe-agent-dispatcher.py), 2026-06-19"
schema_version: 1
---

# DP.M.355 — Рефлекс-routing: classify() обязан предшествовать invoke_claude()

## Описание

В двухконтурном диспетчере (рефлекс + LLM) существует критический инвариант исполнения: `classify()` (lookup по executor_catalog.yaml по сигнатуре признаков) должна вызываться **до** `invoke_claude()`.

Если classify() внутри LLM-обработчика или после него — рефлекс-маршрут не экономит токены и не снижает latency. Смысл двухконтурной архитектуры теряется.

Дополнительный инвариант: LLM-путь получает `confidence=0.0` (неопределённая уверенность), а не `confidence=1.0` хардкодом.

## Algorithm

### Step 1: Classify first

```python
def dispatch(request: AgentRequest) -> AgentResponse:
    # ОБЯЗАТЕЛЬНО ПЕРВЫМ — до любого LLM-вызова
    match = classify(request, executor_catalog)
    
    if match.is_reflex:
        return execute_reflex(match.handler, request)
    
    # Только если рефлекс не сработал — LLM
    return invoke_claude(request, confidence=0.0)
```

### Step 2: Проверь confidence для LLM-пути

LLM имеет неопределённую уверенность по определению:
```python
# Правильно
return AgentResponse(result=llm_result, confidence=0.0)

# Неправильно (хардкод)
return AgentResponse(result=llm_result, confidence=1.0)
```

### Step 3: Smoke-test инварианта

```python
def test_reflex_classify_before_invoke():
    """При рефлекс-match — invoke_claude НЕ должен вызываться."""
    with mock.patch('dispatcher.invoke_claude') as mock_llm:
        dispatch(reflex_request)
        mock_llm.assert_not_called()  # Инвариант держится
```

## When to use

- Реализация/рефакторинг диспетчера агентов с двумя контурами
- Code review dispatcher-кода: проверить порядок classify → invoke
- Дизайн нового типа агентского маршрута

## Тест применимости

«При рефлекс-match — вызывается ли LLM?»
- **Нет** → classify() работает до invoke_claude(), инвариант держится
- **Да** → classify() вызывается после или внутри LLM-пути, рефлекс не работает

## Anti-patterns

- **classify() внутри LLM-хендлера**: рефлекс-логика недоступна до старта LLM — токены тратятся всегда
- **confidence=1.0 для LLM-пути**: LLM по определению не знает уверенности, хардкод 1.0 вводит в заблуждение downstream-потребителей метрик
- **Отсутствие smoke-test**: инвариант нарушается при рефакторинге без теста

## Связи

- DP.M.170 — router-role-dispatch-separation (разделение ролей router и executor)
- DP.M.112 — run-skill-headless-dispatch (паттерн headless диспатча скилла)
- DP.D.075 — personal-search-vs-honcho-roles (контекст двухконтурной архитектуры)
