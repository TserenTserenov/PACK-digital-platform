---
id: DP.D.145
name: "Probe-канал (прямая проверка) ≠ Cascade detection (обнаружение через следствия)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-15
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.145: Probe-канал (прямая проверка) ≠ Cascade detection (обнаружение через следствия)

| Probe-канал | Cascade detection |
|-------------|-------------------|
| Напрямую проверяет корневую зависимость | Обнаруживает корень через downstream-эффекты |
| Время обнаружения: ≤ период probe (N мин) | Время обнаружения: время до жалобы или падения метрики |
| Явный сигнал: «LLM отдала != 200» | Пассивный: «ошибки в боте, DAU=0» |
| SLA: предсказуемый | SLA: непредсказуемый |

**Почему важно:** Cascade detection пассивен и отстаёт — каждый downstream-сигнал указывает на следствие, а не корень. Пример: 403 от внешней LLM обнаружился через downstream с задержкой ~1ч. Прямой probe ловит корень за ≤N мин.

**Тест:** «Есть ли канал, который проверяет корневую зависимость напрямую, а не её следствия?» Нет → детектирование сбоя = время до жалобы.

**Применение:** для каждой критической внешней зависимости (LLM, БД, payment-provider, auth) — отдельный прямой probe-check.

**Связано с:** [DP.D.146] (Бизнес-алерт ≠ Технический алерт), [[HTTP status check ≠ keyword check]], [[Внутренний алерт (failure) ≠ Внешний heartbeat (dead-man's switch)]].

**Источник:** peer-session 2026-06-15-23-ke-candidates-review.
