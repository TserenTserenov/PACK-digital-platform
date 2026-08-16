---
id: DP.M.303
name: "Production DDL через gated-шаг: отдельный .sql файл вне application code"
type: method
status: draft
created: 2026-06-12
trust:
  F: 3
  G: domain
  R: 0.8
epistemic_stage: established
sources:
  - session-close 2026-06-11, WP-417 Ф3.2 (panel_store.py:ensure_schema, scripts/sql/wp417-panel-schema.sql, eeaee81d6)
tags: [database, DDL, schema, deployment, security, gating, migration, postgresql]
wp: WP-417
---

# DP.M.303 — Production DDL через gated-шаг: отдельный .sql файл вне application code

## 1. Принцип

**Application code знает про схему (использует таблицы), но не вправе её менять.**
DDL в проде — отдельный путь развёртывания с явным гейтом.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Удобство автосоздания схемы ↔ контроль изменений в проде | `CREATE TABLE IF NOT EXISTS` из app startup экономит ручной шаг деплоя, но лишает прод явной точки review для DDL-изменений |
| Единый код-путь для dev/prod ↔ разные требования к правам | Один и тот же `ensure_schema()` для обоих окружений упрощает поддержку, но dev не имеет ограничений на DDL-права, а прод — имеет |
| Скорость первого запуска ↔ предсказуемость race condition | Auto-DDL на первом обращении работает мгновенно в single-instance dev, но при concurrent старте нескольких prod-инстансов создаёт гонку за создание таблицы |
| App-credentials минимальные ↔ app-credentials с DDL-правами | Отказ от DDL-прав в app-credentials сужает attack surface, но требует отдельного канала (CI/CD, ответственный) для применения схемы |

## 2. Паттерн

```python
def ensure_schema(self):
    if self.is_pg:
        # В проде: DDL применяется отдельным gated-шагом (psql -f scripts/sql/schema.sql)
        # ensure_schema() в prod — no-op; схема уже применена
        pass
    else:
        # В dev/test: применяем DDL напрямую из того же .sql файла
        self._apply_sqlite_schema()
```

**Prod DDL:** `scripts/sql/<wp>-schema.sql` — применяется явным `psql -f` через CI/CD gated-шаг или ответственным.

## 3. Тест корректности

> «Может ли первый запрос приложения к свежей БД (без предварительного gated-шага) создать таблицу?»

- **Да** → DDL живёт в application path → нарушение.
- **Нет** → паттерн соблюдён.

## 4. Антипаттерн

`CREATE TABLE IF NOT EXISTS` из app startup в проде:
- Невидимое расхождение схем между инстансами (race при concurrent start)
- App-credentials требуют права DDL → расширение attack surface (B7.3)

## 5. Связь с dev/test

Тот же `.sql` файл используется в dev через `ensure_schema()`. Один источник истины для схемы в обоих окружениях.

## 6. Применимость

Любое приложение PostgreSQL в проде + SQLite/in-memory в dev. Особенно critical при Railway/Neon деплоях.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| «`IF NOT EXISTS` — стандартная защита от повторного запуска» | Внимание фокусируется на идемпотентности (не упадёт при повторном вызове), упускает, что вопрос не «упадёт ли», а «кто и когда решает менять схему прода» |
| «В dev и проде один код — значит один процесс развёртывания» | Практикующий переносит ментальную модель dev (запустил — сработало) на прод, не замечая, что там это должно быть двумя разными путями |
| «Схема стабильна, миграций больше не будет» | Гейт кажется избыточным для «финальной» схемы; при первом же реальном изменении схемы выясняется, что явного процесса применения DDL нет |
| «CI зелёный — значит DDL применился корректно» | Тесты на SQLite проходят при отсутствии реального проверки применения `.sql`-файла к prod PostgreSQL через gated-шаг |

## 7. Связи

- DP.M.302 — trusted-reference хранилище (использует этот паттерн для panel schema)
