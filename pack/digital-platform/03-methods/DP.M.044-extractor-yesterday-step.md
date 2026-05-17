---
id: DP.M.044
type: method
status: active
summary: "Extractor Yesterday — паттерн замыкания knowledge pipeline: Day Open явно включает просмотр captures экстрактора за вчера как обязательный шаг до начала новой работы. Без этого шага captures попадают в inbox, но не в фокус сессии."
created: 2026-05-15
valid_from: 2026-05-15
epistemic_stage: implemented
trust:
  F: 1
  G: domain
  R: 0.8
related:
  uses: []
  informs: []
source: "WP-FMT feat day-open шаг 5c2 «Экстрактор вчера», 2026-05-15"
---

# DP.M.044 — Extractor Yesterday (Экстрактор вчера как шаг Day Open)

## Назначение

Замыкает knowledge pipeline: выход предыдущего цикла (captures экстрактора) становится явным входом следующего цикла (Day Open).

## Проблема

Без явного шага «Экстрактор вчера»:
- Captures попадают в `inbox/captures.md` (экстрактор отработал)
- Но НЕ попадают в фокус текущей сессии
- Знание теряется до следующего `/apply-captures`

## Механизм

```
Day Open шаг 5c2 «Экстрактор вчера»
    ↓
Агент читает extraction-reports/ за вчера (status: analyzed)
    ↓
Выводит резюме: N captures ожидают review
    ↓
Pilot видит pending knowledge → может решить: apply сейчас или запланировать
    ↓
Knowledge pipeline замкнут
```

## Принцип

**Knowledge pipeline замыкается только если входной шаг (Day Open) явно потребляет выход предыдущего цикла (inbox-check).** Асинхронные фоновые процессы без явного входа создают «тихий накопитель» — знание формально собрано, но не введено в рабочий контекст.

## Применимость

Любой workflow с фоновым агентом (extractor, auditor, monitor), результаты которого должны влиять на следующую сессию. Паттерн: «вход дня = выход ночи».

## Failure modes

- Нет extraction-reports/ за вчера → шаг пропускается без ошибки (graceful skip)
- Слишком много pending → агент выводит только count + summary, не весь список

## Связи

- **DP.SC.036** (Knowledge Routing Gate) — экстрактор работает по этому SC
- **AS.M.011** (Headless scheduled agent) — экстрактор = headless agent с inbox-check
