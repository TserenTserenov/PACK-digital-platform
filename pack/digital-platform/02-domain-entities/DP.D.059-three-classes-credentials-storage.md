---
id: DP.D.059
name: "Три класса хранения credentials при ротации"
type: distinction
status: active
valid_from: 2026-05-14
source: "Инцидент 2026-05-14: projection-worker stall 63K backlog после ротации Neon password"
---

# DP.D.059: Три класса хранения credentials при ротации

| Класс | Где живёт | Как обнаружить | Почему пропускают |
|-------|-----------|----------------|-------------------|
| **Env vars (1)** | Railway env, tsekh-1 /etc/iwe/env, CF Workers secrets | grep по имени роли / DSN | Видимый, привычный — проверяют первым |
| **Config files (2)** | .env, .secrets/, конфиг-файлы в FS | `grep -rE "<role>" ~/IWE/` | grep по FS выглядит исчерпывающим — останавливаются здесь |
| **PG USER MAPPING (3)** | Metadata внутри Postgres (FDW / postgres_fdw) | `SELECT srvname FROM pg_foreign_server` в каждой БД | **Невидим снаружи БД** — grep файлов не находит |

## Почему важно

После обновления классов 1+2 и redeploy ошибка меняется: `password authentication failed` → `could not connect to server "reference_srv"`. Смена формулировки ошибки — диагностический сигнал пропущенного класса 3 (FDW). Без явного sweep PG USER MAPPING ротация credentials считается завершённой ошибочно.

## Тест полноты ротации

1. После redeploy нет `password authentication failed`? ✓ Класс 1 закрыт.
2. `grep -rE "старый_пароль" ~/` пустой? ✓ Класс 2 закрыт.
3. `SELECT srvname FROM pg_foreign_server` в каждой БД пустой или все USER MAPPING обновлены? ✓ Класс 3 закрыт.

## Применимо

Любой Postgres с `postgres_fdw` / `dblink`. Проверять во всех БД (не только «подозрительных»): только БД rewards имела 4 FDW-сервера к reference — нашлось поиском, не по памяти.

## Связь

- `lessons_rotate_password_grep_first.md` (классы 1+2 + runbook + класс 3 sweep step)
- Инцидент 2026-05-14: projection-worker stall 63K backlog после ротации Neon password.
