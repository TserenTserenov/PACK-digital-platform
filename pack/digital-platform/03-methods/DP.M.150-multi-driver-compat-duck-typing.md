---
id: DP.M.150
name: "Multi-Driver Compat via Duck-Typing of Connection API"
name_ru: "Совместимость нескольких драйверов БД через duck-typing коннекта"
type: method
status: draft
created: 2026-05-22
trust:
  F: 3
  G: domain
  R: 0.8
epistemic_stage: established
related:
  uses: []
  see_also: []
tags: [python, database, compat, duck-typing, psycopg, cross-version]
wp: WP-200 Ф7 (2026-05-22)
---

# Совместимость нескольких драйверов БД через duck-typing коннекта (DP.M.150)

## 1. Проблема

Скрипт деплоится в гетерогенный pool окружений (Python 3.9 vs 3.14), где доступны разные версии DB-драйвера (psycopg2-binary vs psycopg). Нужна единая кодовая база без разных файлов или жёстких проверок версии.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Единая кодовая база ↔ богатство API нового драйвера | Правило пересечения сознательно отказывается от copy-протокола psycopg3, встроенного async и pipeline `executemany` — скрипт платит функциональностью за работу на всём pool окружений без веток и отдельных файлов |
| Дешевизна try-import ↔ контролируемость выбора драйвера | Fallback по `ImportError` не требует ни проверок версии, ни конфигурации, но выбор драйвера определяется средой, а не кодом — контроль за тем, что пересечение API действительно соблюдено, переносится в CI matrix 3.9/3.14 |

## 2. Паттерн

**Try-import с fallback + использование только пересечения API:**

```python
try:
    import psycopg as pg          # psycopg3 (Python 3.14+)
except ImportError:
    import psycopg2 as pg         # psycopg2 (Python 3.9)

# Далее — только общий subset API
with pg.connect(dsn) as conn:
    with conn.cursor() as cur:
        cur.execute("SELECT ...", (param,))  # positional params
        row = cur.fetchone()
```

**Правило пересечения:** использовать только методы, присутствующие в обоих драйверах:
- `connect(dsn)`, `cursor()`, `execute()`, `fetchone()`, `fetchall()`
- Контекстные менеджеры (`with conn`, `with cur`)
- Positional params (`%s`), не named (различаются)

## 3. Где API расходится (требует abstraction)

| Фича | psycopg2 | psycopg3 |
|------|----------|----------|
| Named params | `%(name)s` | `%(name)s` (совпадает) |
| Copy protocol | `copy_expert()` | `copy()` — разные |
| Async | asyncpg отдельно | встроен |
| `executemany` batch | по-строчно | pipeline |

Расходящиеся части → abstraction layer или выбор одного стиля.

## 4. Когда применять

- Скрипт деплоится в гетерогенный pool (старый прод + новый dev).
- Должен работать у внешних пользователей с любым Python ≥ 3.7.
- Используется только базовый subset SQL-операций.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Успешный запуск в своём окружении затмевает CI matrix | Практик проверяет скрипт на своём Python и считает совместимость доказанной, а matrix 3.9/3.14 — единственное место, где пересечение API проверяется на обоих драйверах, — откладывается или настраивается на одну версию |
| Точка выбора драйвера затмевает тело скрипта | Внимание приковано к блоку try-import как к «месту совместимости», а дальше по коду незаметно проскальзывают psycopg3-only вызовы (`copy()`, pipeline `executemany`) — пересечение нарушается не в точке импорта, а в обычных запросах |

## 5. Антипаттерны

- **Условные ветки `if sys.version_info`** — хрупко при ручной установке драйверов.
- **Отдельные скрипты** на каждую версию — дублирование логики.
- **pip-маркеры с обоими драйверами** — тащит лишний драйвер в обе среды.

## 6. Проверка

Запустить в CI matrix:
```yaml
python-version: ["3.9", "3.14"]
```

## 7. Связи

- DP.M.033 — matrix-CI template testing (CI matrix для проверки паттерна)

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 5). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
