---
id: DP.FM.024
name: git-pull-in-production — слияние build/release/run в агентах и launchd
type: failure-mode
domain: DP
status: active
valid_from: 2026-05-12
source: WP-307 Ф5, 12-factor F5
---

# DP.FM.024 — git-pull-in-production: отсутствие immutable artifact

## Паттерн

Автономные агенты (A1-A6) и launchd/systemd-сервисы используют `git pull && python script.py` как деплой. Это сливает три 12-factor стадии в один шаг:
- **Build** (получить код) + **Release** (настроить) + **Run** (запустить)

## Последствия

- Некорректный коммит в `main` немедленно попадает в production
- Нет окна для верификации до запуска
- Нет immutable artifact — невозможно откатить без `git revert`
- Нет версионирования запущенного кода (нельзя узнать, какой коммит работает)

## Отличие от Railway/CF Workers

Railway и Cloudflare Workers имеют platform-level immutable artifacts и rollback (кнопка). Агенты на launchd — нет.

## Паттерны защиты

1. **PIN коммита:** скрипт явно указывает `git checkout <SHA>` вместо rolling pull
2. **Отдельный build-шаг:** `make build` до `make run`
3. **Health-check на SHA:** `/health` возвращает `{"commit": "abc123"}` для версионирования
4. **Smoke-test перед переключением:** новая версия проходит smoke — только потом старая останавливается

## Тест-вопрос

Можно ли откатить агента без `git revert`? Если нет → нарушение 12-factor F5.

## Связи

- 12-factor F5: Build / Release / Run
- Контекст: WP-307 Ф5 (12-factor posture audit, 12 мая 2026)
- Смежно: DP.FM.009 (hardcoded script path)
