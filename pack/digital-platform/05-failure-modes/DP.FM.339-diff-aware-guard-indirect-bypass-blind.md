---
id: DP.FM.339
name: "Diff-aware guard слеп к indirect-обходу (запрещённый вызов рождается в рантайме через переменную/генерацию)"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-06-28
created: 2026-07-18
source: "peer-session 2026-06-26-26 WP-436, turn 1; git diff за сессию; extraction-report 2026-06-28-inbox-check #4"
related:
  references: [DP.D.255, DP.FM.113]
  see_also: [DP.D.255]
tags: [security-guard, diff-aware, indirect-bypass, runtime-generation, pre-commit]
---

# DP.FM.339 — Diff-aware guard слеп к indirect-обходу через переменную/генерацию

## Паттерн

Delta-aware/diff-ориентированный гард ловит литеральный запрещённый вызов в строках diff, но пропускает тот же вызов, собранный в рантайме через переменную, `envsubst`, `make`, `just` или кодогенерацию.

## Пример

```bash
# Прямой вызов — гард ЛОВИТ:
bash scripts/overnight-auditor.sh

# Indirect через переменную — гард НЕ ЛОВИТ:
RUNNER="scripts/${NIGHTLY_TASK}.sh"
$RUNNER  # запрещённый вызов возникает только при выполнении
```

## Механизм

1. Гард ищет запрещённый паттерн построчно по diff
2. Строка `RUNNER="scripts/${NIGHTLY_TASK}.sh"` не содержит запрещённого паттерна — гард молчит
3. Запрещённый вызов возникает только при выполнении: pre-commit и CI не видят его
4. Гард возвращает «чисто», обход проходит в main незамеченным

## Почему опасен

- Diff-aware grep — стандартный выбор для pre-commit и CI security-гардов
- Класс indirect-обходов (переменная, `make`, `just`, кодогенерация) систематически проходит мимо
- Защита выглядит работающей — до целенаправленного тестирования

## Лечение

1. **Семантически второй слой** (DP.D.255) — статический анализ потока данных (taint-analysis) или full-repo аудит точек входа
2. **Red-team bypass-тест** — явный regression-тест с indirect вызовом; класс без теста = непокрытый вектор
3. **Аудит на каждый push в main** — охватывает direct push и post-merge drift, мимо которых проходит PR-гард

## Связи

- DP.D.255 (один механизм в двух местах ≠ два слоя) — смежный архитектурный дефект
- DP.FM.113 (regex глотает второе нарушение) — аналог по классу: regex-гард с blind spot; другой механизм
