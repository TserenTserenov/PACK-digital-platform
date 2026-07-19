---
id: DP.D.088
name: "`environment.d` (декларативный, persistent) ≠ `systemctl --user set-environment` (императивный, ephemeral)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-05-24
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.088: `environment.d` (декларативный, persistent) ≠ `systemctl --user set-environment` (императивный, ephemeral)

| Аспект | `~/.config/environment.d/*.conf` | `systemctl --user set-environment KEY=VALUE` |
|--------|----------------------------------|----------------------------------------------|
| **Момент применения** | При старте user-сессии (login/linger=yes) | Немедленно |
| **Персистентность** | Между сессиями (файл на диске) | Теряется при logout/reboot |
| **Существующие unit'ы** | НЕ видят переменную до перезапуска | Видят немедленно |
| **Тип** | Декларативный (конфиг-файл) | Императивный (команда) |
| **Проверка** | `cat /proc/$(pidof <agent>)/environ` | `systemctl --user show-environment` |

**Правильный паттерн для headless-агента:**
1. Записать в `environment.d` (персистентность между reboot)
2. Выполнить `set-environment` (немедленный эффект без рестарта сессии)
3. `daemon-reload + restart` затронутых unit-файлов

**Тест применения:** «Переменная появилась в `systemctl --user show-environment`?» → `set-environment` отработал. «Появилась в `/proc/$(pidof <agent>)/environ`?» → unit перезапущен после загрузки `environment.d`.

**Почему важно:** Только `set-environment` — потеря переменной после reboot. Только `environment.d` — новая переменная не видна до полного session restart. Для headless-агентов с `linger=yes` оба механизма обязательны.

**Контекст:** Выявлено при деплое агентов на tsekh-1 (WP-200 Ф7, 2026-05-22). Применимо ко всем remote-headless deployments через user systemd.
