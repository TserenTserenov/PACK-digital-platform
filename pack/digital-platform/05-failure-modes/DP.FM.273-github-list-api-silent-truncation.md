---
name: GitHub List API обрезает результат при превышении page size без явной ошибки
id: DP.FM.273
domains: [github-api, pagination, api-integration]
tags: [implicit-pagination, silent-truncation, github-app]
severity: high
---

# DP.FM.273 — GitHub List API обрезает результат без явной ошибки

## Суть

GitHub list-эндпоинты (например, `GET /app/installations`) возвращают первые N (30 или 100) записей без явной ошибки, если не запрошена пагинация. Запись сверх первой страницы становится невидима для кода без paginated-обхода. Симптом: API возвращает 200, результат непустой, но конкретная запись отсутствует — выглядит как «not found», а не как пагинационный обрыв.

## Инцидент

WP-149 (commit b1d897e): `GET /app/installations` не находил installation для TserenTserenov при базе >30 аккаунтов — запись была за пределами первой страницы.

## Fix

Обязательный paginated-обход: `per_page=100&page=N` в цикле до пустой страницы.

```python
installations = []
page = 1
while True:
    page_data = api.get(f"/app/installations?per_page=100&page={page}")
    if not page_data:
        break
    installations.extend(page_data)
    if len(page_data) < 100:
        break
    page += 1
```

## Паттерн

**Тест:** «API возвращает массив без `total_count` и без `next` в `Link`-заголовке?» → обязательно явно перебирать страницы до пустой.

Применимо ко всем GitHub list-эндпоинтам (installations, repos, teams, members) и аналогичным API с implicit pagination (PlanetScale, Linear, Notion).

**Источник:** session-close 2026-07-08 (commit b1d897e, WP-149).
