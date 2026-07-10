---
id: DP.METHOD.148
type: method
domain: PACK-digital-platform
status: draft
summary: "Rest state companion/маскота = отсутствие действия, не новое эмоциональное состояние. Любой visible-сигнал покоя (пыль, потускнение) создаёт guilt trigger → disengagement."
created: 2026-06-27
valid_from: 2026-06-27
version: v1.0
source: "session-transcript 2026-06-26-13; WP-292 peer Ф9a mascot spec"
related:
  see_also: [DP.D.221]
---

# DP.METHOD.148: Rest State = Silence (companion/маскот в edtech)

## Принцип

Покой маскота = **тишина действий**, без нового визуального или эмоционального состояния.

## Механизм провала

```
visible rest signal (пыль/потускнение/грусть)
→ пользователь читает: "маскот грустит = я виноват"
→ guilt trigger
→ anxiety при пропуске
→ пользователь отключает фичу или избегает открывать приложение
```

## Правило дизайна

- **Покой**: ноль визуального изменения. Последний animation frame застывает на месте.
- **Работа**: анимация/эффект активны.
- **Различение**: покой ≠ работа только через **отсутствие движения**, не через смену настроения.

## Тест

«Если пользователь не открывал приложение 3 дня — маскот выглядит иначе?» → Да → риск guilt trigger. Нет → correct.

## Применимость

- Companion/indicator в edtech и productivity-приложениях
- Streak-счётчики, progress bars — нейтральное состояние при паузе
- Нарушение: Tamagotchi-механика (деградация при паузе) = guilty rest state → disengagement

## Связи

- Смежное различение: DP.D.221 (качественный порог мастерства ≠ счётчик очков/частоты)
- Принцип: absent action ≠ sad state (для любого companion)
