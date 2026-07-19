---
id: DP.D.106
name: "Trigger по состоянию ≠ Trigger по счётчику (для архитектурных переключений)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-01
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.106: Trigger по состоянию ≠ Trigger по счётчику (для архитектурных переключений)

**State-based trigger** — переключение запускается при появлении **первого** объекта out-of-current-scope (первый non-Mac пилот; первый T2+ user; первый клиент в новом geo). Срабатывает до накопления tech-debt'а.

**Counter-based trigger** — переключение запускается, когда счётчик достигает порога («когда users > 20»). К моменту срабатывания accumulated cost от обходных решений обычно превышает migration cost.

| Аспект | State-based | Counter-based |
|--------|-------------|---------------|
| **Trigger** | Появление out-of-scope объекта | Достижение N |
| **Накопление debt'а** | До trigger ≈ 0 | Линейный рост до threshold |
| **Migration cost** | Стабильный | Растёт с каждым добавленным workaround'ом |
| **Подходит для** | Качественных сдвигов (новая платформа, рефакторинг, абстракция) | Инкрементальной стоимости (capacity scaling) |

**Тест выбора:** «Растёт ли debt линейно с N или есть точка качественного сдвига?» Качественный сдвиг → state-based. Линейный рост ёмкости/нагрузки → counter-based.

**Failure mode:** counter-based для качественного сдвига → к trigger N=20 уже написано 3 OS-specific install-скрипта, миграция дороже, чем сделать extension при N=1.

**Применимо к:** решениям о вводе абстракций, замене стека, миграции с PoC на production stack, кросс-платформенным архитектурам.

**Источник:** session-transcript 2026-05-27 (peer-сессия 21 wp358-archgate, Тема 4 — switching B → A).
