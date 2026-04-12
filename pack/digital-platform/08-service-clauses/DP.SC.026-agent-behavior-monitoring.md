---
id: DP.SC.026
name: "Мониторинг поведения агента"
layer: L4-Personal
status: draft
created: 2026-04-12
related:
  extends: [DP.SC.025]
  depends_on: [DP.FM.010, DP.FM.011]
  implemented_by: [WP-229]
tags: [agent, monitoring, gates, capture-bus, incident-log, week-close]
---

# [DP.SC.026] Мониторинг поведения агента

## Обещание (инвариант)

Система обнаруживает паттерн провала агента → фиксирует событие в журнале → на Week Close предъявляет аналитическую сводку с рекомендацией действия.

**Гарантия:** если провал обнаружен детектором, он попадёт в incident-log. Если гейт сработал — попадёт в gate_log с полем `pattern`. На Week Close сводка формируется из этих журналов.

**Ограничение:** система ловит то, что умеют детекторы (P1, P3 в v1). Непойманные паттерны (P1b — «пронесло») требуют R8-R9 вопросов в R-вопроснике.

## Триггер

Автоматический: каждый PostToolUse / Stop — capture-bus запускает зарегистрированные детекторы.
Ручной: Week Close — агент читает gate_log и incident-log, формирует сводку по шаблону R-вопросника.

## Входы

| Вход | Источник |
|------|---------|
| PostToolUse / Stop JSON | Claude Code harness |
| gate_log.jsonl | `.claude/logs/gate_log.jsonl` |
| incident-log-YYYY-MM.md | `<repo>/docs/incidents/` или `<repo>/C2.3.Operations/Incidents/` |

## Выходы

| Выход | Куда | Когда |
|-------|------|-------|
| Запись в incident-log (agent_incident) | Целевой репо | При срабатывании детектора |
| Запись в gate_log (gate_fired) | `.claude/logs/gate_log.jsonl` | При срабатывании гейта |
| Аналитическая сводка | Week Report (раздел «Журналы гейтов и инцидентов») | Week Close |
| Рекомендация: warn→block / создать детектор | В сводке | Week Close |

## Время отклика

- Детектор: ≤150 ms (латентный бюджет capture-bus, warn-before-block)
- Гейт: синхронный (Stop hook, не блокирует при `action=warn`)
- Сводка Week Close: формируется агентом по запросу (~2 мин)

## Инварианты

1. **warn-before-block:** новый детектор/гейт стартует в `action=warn`. Промоция в `block` только после 2 нед обкатки при fire rate > 0 И FP < 10%.
2. **OwnerIntegrity:** инцидент пишется в репо, где произошёл провал. Сводка — агрегация, не первоисточник.
3. **Graceful degradation:** сбой детектора не блокирует сессию. capture-bus exit 0 всегда.
4. **Паттерн в каждой записи:** gate_log содержит поле `pattern: P{N}`. incident-log содержит `pattern` в payload. Записи без паттерна — кандидаты P1b.
5. **Самоизмерение:** если `count(P1 incidents) > 0` за 2 нед → detector_pattern_awareness не работает или правило неверно.

## Режимы отказа

| Ситуация | Последствие | Митигация |
|----------|-------------|-----------|
| Детектор не запускается | Паттерн не пойман | `bash -n` при деплое, capture_log проверка статуса |
| FP rate > 10% | Шум → доверие к системе падает | R8 вопрос в Week Close, откат гейта |
| gate_log не читается агентом в Week Close | Сводка пустая | Агент явно читает gate_log в шаблоне Week Close |
| Инцидент без `pattern:` | Не агрегируется по паттерну | R9 вопрос + DP.FM.011 §Correction |

## Сценарии использования

### Сценарий 1: Детектор поймал P1

Агент пишет в `feedback_behaviour.md` новое правило без ссылки `pattern: P{N}`.
→ detector_pattern_awareness срабатывает PostToolUse.
→ capture-bus → capture_writer → incident-log: запись с `pattern: "P1_not_capturing"`, description с DP.FM.011 §Correction.
→ На Week Close: сводка показывает P1=1. R9 вопрос: «есть ли паттерн, которого нет в DP.FM.010?» → человек решает, добавить ли в каталог.

### Сценарий 2: Гейт заблокировал P9

Агент запускает `day-close` скилл без TodoWrite ≥3 items.
→ protocol-stop-gate.sh сработал Stop hook.
→ gate_log: запись `{fired: true, pattern: "P9", action: "warn"}`. Агент видит предупреждение, создаёт TodoWrite, проходит протокол.
→ На Week Close: gate_log показывает fire=1, FP=? (R8 вопрос). Если паттерн реальный → через 2 нед action=block.

### Сценарий 3: Пользователь заметил инцидент

Пользователь видит, что агент пропустил шаг. Нет детектора для этого паттерна.
→ Пользователь говорит «это DP.FM.010 паттерн P{N}».
→ Агент добавляет запись в incident-log вручную с `pattern: P{N}`.
→ На Week Close: если 3+ таких записей → создать детектор.
