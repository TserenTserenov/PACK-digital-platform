---
id: DP.FM.023
type: failure-mode
name: service-user-credentials-path
title: "Service user credentials: секреты в $HOME сервисного пользователя, не /root"
domain: digital-platform
pack: PACK-digital-platform
valid_from: 2026-05-12
status: active
schema_version: 1
---

# DP.FM.023 — Service User Credentials Path

## Симптом

Сервисный процесс (systemd, launchd) запускается от имени выделенного пользователя (напр. `tsekh-1`), но OAuth credentials, токены или конфигурационные файлы хранятся в `/root/.config` или `/root/.credentials`. Скрипт молча не находит токен → авторизация падает без явного сообщения об ошибке.

## Причина

При ручной настройке OAuth/credentials разработчик выполняет команды от `root` → файлы попадают в `/root/`. Сервисный пользователь не имеет доступа к `/root/`.

## Паттерн защиты

1. **Создавать credentials только от имени сервисного пользователя:**
   ```bash
   sudo -u tsekh-1 gcloud auth login
   sudo -u tsekh-1 oauth-tool setup
   ```
2. **Проверять `$HOME` явно перед настройкой:**
   ```bash
   sudo -u tsekh-1 bash -c 'echo $HOME'  # → /home/tsekh-1, не /root
   ```
3. **В systemd-юните:** `User=tsekh-1` + `Environment=HOME=/home/tsekh-1`

## Применимость

Любые сервисные пользователи с OAuth или file-based credentials: Google Calendar, GitHub App, Telegram Bot. Особенно критично при ночных агентах, где ошибка не видна интерактивно.

## Связи

- Память (реализация): `memory/lessons_creds_per_user_home.md`
- Смежный паттерн: DP.FM.022 (systemd minimal PATH)
