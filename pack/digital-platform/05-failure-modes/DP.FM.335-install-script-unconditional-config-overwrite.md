---
id: DP.FM.335
name: "Install/update скрипт безусловно перезаписывает конфиг пользователя"
type: failure-mode
status: active
valid_from: 2026-07-11
source: "WP-5 settings.json clobber; FMT-exocortex-template commit 261c5a0"
related:
  see_also: [DP.FM.115]
tags: [install, update, config, overwrite, seed-if-absent, customization]
---

# DP.FM.335 — Install/update скрипт безусловно перезаписывает конфиг пользователя

## Симптом

Пользователь добавил кастомизацию в конфигурационный файл (security-хуки, правила, расширения). После очередного `update.sh` / `repair.sh` кастомизация пропала без предупреждения и без ошибки.

## Механизм

```bash
# Антипаттерн: безусловная перезапись
cp default_settings.json .claude/settings.json

# Фикс: seed-if-absent
[[ -f .claude/settings.json ]] || cp default_settings.json .claude/settings.json
```

Скрипт не проверяет, существует ли файл уже. Команда «overwrite» стирает любые изменения пользователя с момента установки.

## Отличие от DP.FM.115

| Аспект | DP.FM.115 | DP.FM.335 |
|--------|-----------|-----------|
| Субъект | Peer-агент (runtime) | Install/update скрипт (deployment) |
| Контекст | Shared-файл при параллельной работе | Конфигурационный файл при обновлении |
| Симптом | Потеря контента при записи | Потеря кастомизаций при update |

## Фикс

Три стратегии, в порядке предпочтения:
1. **seed-if-absent**: скрипт пишет файл ТОЛЬКО если он отсутствует
2. **merge**: скрипт делает merge платформенных defaults + пользовательских значений
3. **backup-and-replace**: скрипт сохраняет старый файл перед заменой и сообщает о конфликте

## Тест

«Есть ли у пользователя право расширять этот конфиг-файл?» Да → команда `cp` / `echo >` без проверки = FM. Seed-if-absent или merge = фикс.

«Пользователь жалуется "пропало правило", а в diff его нет» → вероятно DP.FM.335.
