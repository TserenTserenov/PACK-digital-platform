---
id: DP.FM.169
name: "Тихий fallback в content pipeline: acceptance PASS при деградации содержания"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-24
source: "session 2026-06-24 peer-02 WP-149; acceptance PASS 2026-06-20 при схлопывании руководства"
related:
  references: [DP.FM.167, DP.METHOD.065]
  see_also: ["distinctions.md: HTTP status check ≠ keyword check", "distinctions.md: Приёмка обещания ≠ Прод-наблюдение"]
tags: [content-pipeline, silent-fallback, acceptance, quality, false-green]
---

# DP.FM.169 — Тихий fallback в content pipeline: acceptance PASS при деградации содержания

## Паттерн

Content generator при отсутствии нужного источника тихо подменяет его дефолтным. Acceptance-тест проверяет наличие output-файла → PASS. Но содержание деградировало незаметно: квалификация потребителя не отражена.

## Пример

```python
# WP-149: генератор руководства ЛР
def build_guide(rung, program_sources):
    src = program_sources.get(rung.program, DEFAULT_PROGRAM_SRC)  # тихий fallback
    return render_guide(src)

# Итог: файл создан → acceptance PASS
# Но: для Реформатора отрендерилось руководство Ученика (DEFAULT_PROGRAM_SRC)
```

## Механизм

1. Нужный источник (`program=reformator`) отсутствует в `program_sources`
2. Генератор использует `DEFAULT_PROGRAM_SRC` (программа Ученика) без предупреждения
3. Acceptance-criterion: «файл создан» → PASS (output-метрика, не outcome-метрика)
4. Деградация содержания обнаруживается только при ручном просмотре или outcome-наблюдении

## Почему опасен

- Output-based acceptance не видит деградацию (false-green)
- Дефект накапливается: пользователь получает неверное руководство, остаётся в системе
- Фаза закрывается как done — дефект «закреплён» в проде

## Лечение

1. **DP.METHOD.065** (verifier-before-assembly): явный список missing_source перед сборкой
2. **Outcome-criterion**: acceptance проверяет соответствие содержания квалификации, не только наличие файла
3. **DP.METHOD.064** (gate:outcome-pending): time-bounded observation period для выявления подобных дефектов

## Связи

- Смягчается: DP.METHOD.065, DP.METHOD.064
- Аналог: [[HTTP status check ≠ keyword check]] — false-green по форме ответа
- Аналог: DP.FM.167 (тихое отключение через флаг) — другой домен, тот же принцип невидимости
