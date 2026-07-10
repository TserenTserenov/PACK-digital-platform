---
id: DP.M.353
name: "Event dedup window in routing catalog"
name_ru: "Параметр окна дедупликации в каталоге маршрутов, не в коде компонента"
name_en: "Event dedup window as routing catalog parameter, not component constant"
summary: "Окно дедупликации события (dedup_window) зависит от семантики типа события, а не от архитектуры компонента. Параметр должен жить в routing_catalog.yaml рядом с маршрутами, не хардкодиться в Учётчике/воркере. Тест: можно ли изменить окно без деплоя компонента?"
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: event-architecture
valid_from: 2026-06-20
related:
  see_also: [DP.M.055, DP.M.199, DP.M.222]
tags: [dedup, event-routing, routing-catalog, config-sot, event-driven, dedup-window]
source: "WP-427 Ф5, peer-session 2026-06-19, Учётчик следов dedup design"
schema_version: 1
---

# DP.M.353 — Параметр окна дедупликации в каталоге маршрутов

## Описание

Окно дедупликации (`dedup_window`) зависит от **семантики типа события**, а не от архитектуры компонента-обработчика. Разные типы событий имеют принципиально разные разумные окна. Если зашить константу в код компонента (Учётчик, воркер), получим false positives для часто повторяющихся событий и пропуски для редких.

Параметр `dedup_window` хранится в routing_catalog.yaml рядом с маршрутом события — там, где уже определён получатель и формат.

## Algorithm

### Step 1: Классифицируй событие по семантике

Определи семантику типа события по частоте и природе повторений:

| Семантика | Пример | Разумное окно |
|-----------|--------|---------------|
| Мгновенное действие | `note_created`, `typing_event` | 30s–2min |
| Сессионное | `session_digest`, `day_summary` | 6h–24h |
| Периодическое | `weekly_reflection`, `monthly_review` | 3d–7d |
| Разовое (идемпотентное) | `subscription_purchased` | 24h–7d |
| Социальное | `club_post`, `comment_added` | 3min–15min |

### Step 2: Добавь dedup_window в routing_catalog.yaml

```yaml
event_routes:
  - event_type: note_created
    handler: learning_worker
    dedup_window: 60s
    
  - event_type: weekly_reflection
    handler: reflection_worker
    dedup_window: 7d
    
  - event_type: club_post
    handler: community_worker
    dedup_window: 5min
```

### Step 3: Компонент читает параметр из каталога

```python
def get_dedup_window(event_type: str) -> timedelta:
    route = routing_catalog.get(event_type)
    return parse_duration(route["dedup_window"])
```

Компонент не знает конкретных значений — только читает из каталога.

## When to use

- Event-driven pipeline с разнородными типами событий
- Компонент обрабатывает ≥2 типов событий с разной частотой
- Требуется изменить окно дедупликации без деплоя компонента

## Тест применимости

«Можно ли изменить окно дедупликации для `club_post` с 5min на 3min без деплоя Учётчика?»
- **Да** → dedup_window правильно в каталоге
- **Нет** → хардкод в компоненте, требует рефакторинга

## Эталон окон (IWE-контекст)

| event_type | dedup_window |
|------------|-------------|
| `note_created` | 60s |
| `session_digest` | 24h |
| `weekly_reflection` | 7d |
| `club_post` | 5min |
| `subscription_first_purchased` | 7d |
| `user_typing_tracked` | 30s |

## Связи

- DP.M.055 — config-sot-triplet (конфиг как source-of-truth — общий принцип)
- DP.M.199 — three-tier-config-parameters (иерархия конфигурации)
- DP.M.222 — event-type-three-component-atomic-deploy (деплой нового event-типа как атомарная единица)
