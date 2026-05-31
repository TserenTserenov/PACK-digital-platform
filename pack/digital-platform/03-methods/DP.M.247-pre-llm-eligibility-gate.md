---
id: DP.M.247
name: Pre-LLM Eligibility Gate
type: method
domain: digital-platform
status: active
valid_from: 2026-05-31
source: DS-autonomous-agents render-pilot-guides.py (commit 73726a3) + peer-session 2026-05-31-16
---

# DP.M.247 — Pre-LLM Eligibility Gate

## Суть

Контент-генератор с неоднородной аудиторией ОБЯЗАН проверять eligibility ДО вызова LLM.
При несоответствии аудитории возвращается информативный stub — не генерация неверного контента.

## Алгоритм

1. Определить множество неподходящих квалификаций/контекстов через константу или конфиг
2. Проверить квалификацию/контекст потребителя ДО вызова LLM
3. При срабатывании gate — вернуть `stub_content()` с описанием: что не реализовано, когда появится
4. LLM-вызов — только при confirmed eligibility

## Пример (Python)

```python
_MSH_QUALIFIED = {"Работник", "Специалист", "Эксперт"}

def render_pilot(pilot_context):
    if pilot_context.get("msh_qualification") in _MSH_QUALIFIED:
        return _msh_stub_content()   # stub, не LLM
    return llm_generate_content(pilot_context)
```

## Применимость

- Многопрограммные системы рендеринга (ЛР/РР/ИР нарративы)
- Онбординговые воронки с дифференцированными этапами
- Конструкторы персональных руководств
- Любой LLM-pipeline с неоднородной входящей аудиторией

## Граница

Не применять когда все потребители однородны или когда содержимое корректно для любой квалификации.

## Связи

- pack_refs: DS-autonomous-agents render-pilot-guides.py
- см. также: DP.M.241 (personal-guide-render) — конкретный consumer этого паттерна
