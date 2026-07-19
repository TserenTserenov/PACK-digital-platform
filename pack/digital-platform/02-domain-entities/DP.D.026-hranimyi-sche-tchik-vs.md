---
id: DP.D.026
name: "Хранимый счётчик ≠ Вычисляемая проекция"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-02-18
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.026: Хранимый счётчик ≠ Вычисляемая проекция

| Хранимый счётчик (mutable counter) | Вычисляемая проекция (computed view) |
|------------------------------------|--------------------------------------|
| Инкрементируется при событии | Вычисляется из журнала событий |
| Хрупкий: race condition, стейлый кеш, сброс при смене контекста | Всегда актуален: SELECT COUNT(*) |
| Требует синхронизации при изменении условий | Автоматически корректен при любых условиях |
| Ошибка накапливается | Ошибка невозможна (один SQL-запрос) |

**Почему важно**: Мутируемый счётчик `topics_at_current_complexity` в `interns` таблице терял значение при смене уровня сложности (reset=0), при race condition (+1 не применялся), при стейловом кеше (user dict не обновлён). Вычисление `COUNT(*) FROM answers WHERE complexity_level=$1` даёт точное значение всегда — answers = журнал фактов (Event Sourcing, SOTA.012).

**Тест**: Значение зависит от N событий и может «сбиться»? → Вычисляй из событий, не храни.

> Источник: баг бота (@TserenTserenov: 5 theory_answer, counter=0). Фикс: `get_theory_count_at_level()` в `db/queries/answers.py`. Связь: DP.D.005 (производный индикатор), SOTA.008+SOTA.012.
