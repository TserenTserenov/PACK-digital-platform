---
id: DP.M.169
name: "Экспериментальный вес с guard-условием для ML-метрики"
type: method
pack: digital-platform
status: active
valid_from: 2026-05-28
source: peer-session 2026-05-28-01 (WP-353 w_honcho hold-out разблокировка)
---

# DP.M.169: Экспериментальный вес с guard-условием

## Метод

Интеграция новой ML-метрики/фичи с явным guard-условием вместо блокировки на «недостаточно данных». Позволяет начать сбор production-данных и обнаружение failure modes без ожидания полного hold-out набора.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Ранний запуск (сбор production-данных и failure modes сразу) ↔ защита агрегата от недокалиброванной метрики | Guard-вес 0.05–0.10 удерживает новую метрику почти безвлиятельной на итог, пока `n < threshold`: система работает в production и данные копятся, но сигнал w_honcho не двигает результат до калибровки |
| Автоматика promotion (снятие guard при n≥threshold без ручного вмешательства) ↔ контроль момента перехода в production-вес | Автоматический upgrade снимает dangling intent блокировки, но ценой того, что порог (`n_threshold: 7`) и целевой вес (0.30) фиксируются в конфиге заранее — до того, как накопленный hold-out покажет реальное качество метрики |

## IPO

**Вход:**
- Новая ML-метрика (w_honcho, A/B-фича, scoring-эвристика)
- Текущий размер hold-out: n < threshold
- Конфигурация: `experimental_threshold`, `experimental_weight`

**Процесс:**
1. Установить флаг `experimental: true` для метрики
2. Определить guard-условие: `if experimental and n < threshold → weight = experimental_weight` (0.05–0.10)
3. Логировать `confidence: low` вместе с результатом
4. При `n >= threshold` — автоматически снять guard (promote флаг `experimental: false`)

**Выход:**
- Система работает в production с минимальным весом метрики
- Накапливаются реальные данные и failure modes
- При достижении threshold — автоматический upgrade без ручного вмешательства

## Пример

```yaml
w_honcho:
  experimental: true
  n_current: 3
  n_threshold: 7
  weight_experimental: 0.05
  weight_production: 0.30
  confidence: low
```

Guard-логика: `weight = 0.05 if (experimental and n<7) else 0.30`

## Ключевые элементы

1. **Явный флаг** `experimental: true` — видим в мониторинге и логах
2. **Guard-threshold в конфиге**, не в коде (изменяем без деплоя)
3. **Логирование confidence** вместе с результатом (аудит)
4. **Автоматическое снятие** guard при n>=threshold

## Применимость

A/B-эксперименты, ML-feature флаги, новые scoring-эвристики, персонализационные весовые модели — везде, где hold-out накапливается постепенно.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Малый вес читается как малая важность | Практик, видя `weight: 0.05` в агрегате и `confidence: low` в логах, перестаёт следить за failure modes метрики («она почти ни на что не влияет») — хотя именно в guard-режиме накапливаются наблюдения, ради которых метрика запущена |
| Guard-режим воспринимается как постоянное состояние | Внимание фиксируется на запуске (флаг, порог, вес), а условия пересмотра самого конфига — адекватен ли `n_threshold: 7` и production-вес 0.30 накопленному hold-out — не отслеживаются: автоматический promote «сам сработает», и качество данных за порогом никто не сверяет |

## Антипаттерн

`status: blocked (waiting for hold-out)` без guard — dangling intent (DP.FM.086). Лечение: experimental guard заменяет блок на минимально-активный режим сбора данных.

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 5). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
