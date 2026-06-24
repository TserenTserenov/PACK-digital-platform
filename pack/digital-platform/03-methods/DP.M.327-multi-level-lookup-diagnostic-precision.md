---
id: DP.M.327
title: "Точность диагностики при многоуровневом lookup: различать причины «не найдено»"
type: method
domain: digital-platform
status: draft
valid_from: 2026-06-23
fpf_parent: U.Method
sources:
  - git diff DS-autonomous-agents 7717eea (fix(wp-149) distinguish area-found-but-empty from no-catalog-entry)
  - WP-149 (каталог рефлексов, диагностика slot→area→text)
related:
  see_also: []
tags: [diagnostic-logging, multi-level-lookup, warning-precision, false-green, engineering]
---

# DP.M.327 — Точность диагностики при многоуровневом lookup: различать причины «не найдено»

## Описание

При многоуровневом поиске (slot→area→text, id→type→content, key→category→text) единственное сообщение «не найдено» скрывает, на каком уровне произошёл сбой. Метод: формулировать отдельные WARNING для каждого уровня отказа.

## IPO

| Вход | Обработка | Выход |
|------|-----------|-------|
| Функция lookup с N уровнями | На каждом уровне отдельная ветка с уникальным сообщением | Отдельные WARNING по уровням: "mapping missing" / "target empty" |

## Алгоритм (для двухуровневого lookup)

```
Уровень 1: slot → area
  Нет записи slot в mapping → WARNING: "No area mapping for slot: {slot}"

Уровень 2: area → text
  Нет текста для area → WARNING: "Area mapping found for slot {slot} → area {area}, but area has no text"
```

Расширение на N уровней: каждый уровень = своя ветка + уникальный message.

## Тест корректности

«Мой warning говорит "не найдено" — он проверяет один уровень или несколько?»  
Если несколько → нарушение метода. Разбить на отдельные ветки.

## Признак нарушения

- Единственный warning «no entry» при multi-level lookup
- Разработчик получает сообщение и не знает: добавить mapping? Добавить content? Проверить оба?

## Практический пример (WP-149)

```python
# До: слитый warning
if slot not in SLOT_TO_AREA or AREA_TO_MEME.get(SLOT_TO_AREA[slot]) is None:
    logger.warning("No catalog entry for slot %s", slot)

# После: раздельные warnings
if slot not in SLOT_TO_AREA:
    logger.warning("No area mapping for slot: %s", slot)
    return None
area = SLOT_TO_AREA[slot]
if not AREA_TO_MEME.get(area):
    logger.warning("Area %s found for slot %s but has no meme text", area, slot)
    return None
```

## Связи

- Родственный FM: false-green при недостаточно точной диагностике (HTTP status check ≠ keyword check)
