---
id: DP.SC.131
name: Автопроцесс резервного копирования данных IWE
version: 1.0
created: 2026-05-15
wp: WP-317
---

# DP.SC.131 — Автопроцесс резервного копирования данных IWE

## Обещание

Система автоматически создаёт и хранит резервные копии всех критичных данных IWE, обеспечивая возможность восстановления после любого сбоя в пределах установленных RPO/RTO.

---

## Триггеры

| Триггер | Расписание | Компонент |
|---------|-----------|-----------|
| Neon БД → Backblaze B2 | ежедневно 03:00 МСК | `restic-backups-neon-dbs.service` |
| Локальные файлы → B2 | ежедневно 03:30 МСК | `restic-backups-local-files.service` |
| ZFS снапшот (часовой) | каждый час | `zfs-snapshot-frequent.timer` |
| ZFS снапшот (дневной) | 00:00 МСК | `zfs-snapshot-daily.timer` |
| ZFS снапшот (недельный) | пн 00:00 МСК | `zfs-snapshot-weekly.timer` |

---

## Входы

- 16 Neon PostgreSQL баз данных (проект `purple-bread-37001042`, branch `production`)
- Локальные файлы: `/home/tseren`, `/etc/restic`
- Credentials: `/etc/restic/neon-connections`, `/etc/restic/password`, `/etc/restic/b2-env`

## Выходы

- Restic-снапшоты в Backblaze B2 bucket `aisystant-neon-backup`
  - repo `neon-dbs`: pg_dump в формате custom (восстанавливается через `pg_restore`)
  - repo `local`: архив локальных файлов
- ZFS снапшоты на пуле `rpool` (локально, in-place)

---

## Гарантии (RPO / RTO)

| Слой | RPO (макс. потеря) | RTO (время восстановления) |
|------|-------------------|--------------------------|
| Neon БД (B2) | 24 часа | ~2 часа (скачать + pg_restore) |
| Локальные файлы (B2) | 24 часа | ~30 минут |
| ZFS снапшот (tsekh-1) | 1 час | ~5 минут (локальный rollback) |
| Git-репо (GitHub) | время последнего push | ~5 минут (git clone) |

**Retention:**
- B2 Neon: 7 ежедневных / 4 еженедельных / 12 ежемесячных снапшотов
- B2 local: 7 / 4 / 3
- ZFS: часовые × 24, дневные × 7, недельные × 4, месячные × 1

---

## Покрытие

### Что входит в бэкап

| Компонент | Механизм | Критичность |
|-----------|----------|-------------|
| persona, payment, subscription, indicators, learning, reference, rewards | Neon → B2 | 🔴 критическая |
| health, community, journal, knowledge, lead, metabase, payment_registry, publication, secrets | Neon → B2 | 🟡 высокая |
| `/home/tseren` (IWE-репо, конфиги, скрипты) | local → B2 | 🔴 критическая |
| Git-репо (Pack, DS, FMT) | GitHub mirrors | 🟢 надёжно |

### Что НЕ входит (известные пробелы)

| Компонент | Причина | Риск |
|-----------|---------|------|
| Google Drive / Gmail / Calendar | vendor lock, нет экспорта | 🟡 средний |
| Railway env vars / secrets | не автоматизировано | 🟡 средний |
| Cloudflare Workers config | wrangler.toml частично в git | 🟡 низкий |

---

## Инвариант

- **Все** 16 Neon БД дампятся каждую ночь без исключений
- Heartbeat в BetterStack пингует **только** при успешном дампе **всех** БД (не при любом завершении)
- Снапшоты с размером 0 байт = аварийный сигнал, не норма

---

## Режим отказа

| Сценарий | Поведение | Детекция |
|----------|-----------|----------|
| Ошибка pg_dump одной БД | Остальные дампятся, heartbeat **НЕ** пингует | Журнал journalctl; BetterStack alert |
| Ошибка всех pg_dump (пароль) | Снапшот 0 байт, heartbeat **НЕ** пингует | BetterStack alert через 30 мин |
| Недоступность B2 | Дамп выполнен локально, restic backup падает | journalctl; BetterStack |
| Отказ tsekh-1 | Neon — в Neon (снапшоты Neon 7 дней), B2 доступен | Недоступность сервера |

---

## Сценарии использования

### СЦ-1: Восстановление после случайного удаления таблицы

**Кто:** Дмитрий (владелец системы)
**Когда:** В процессе работы случайно выполнил `DROP TABLE` или `DELETE` без WHERE
**Действие:**
1. Найти последний успешный снапшот: `restic snapshots --repo b2:aisystant-neon-backup:neon-dbs`
2. Скачать нужный dump: `restic restore <snapshot-id> --target /tmp/restore`
3. Восстановить конкретную таблицу: `pg_restore --table=<имя> -d <целевая_БД> /tmp/restore/<db>.dump`
**RPO:** до 24 часов | **RTO:** ~30 минут

### СЦ-2: Миграция на новый Neon-проект

**Кто:** Команда (Дмитрий + Паша)
**Когда:** Плановая миграция, смена проекта Neon, или disaster recovery
**Действие:**
1. Скачать последний полный снапшот всех 16 БД
2. Создать новый Neon-проект с теми же именами БД
3. Последовательно `pg_restore` каждой БД
4. Обновить connection strings во всех потребителях
**RPO:** 24 часа | **RTO:** ~2 часа

### СЦ-3: Аудит что именно было в системе на конкретную дату

**Кто:** Дмитрий (юридический аудит, разбор инцидента)
**Когда:** Нужно понять состояние данных пользователя или платежа на дату в прошлом
**Действие:**
1. Найти снапшот нужной даты: `restic snapshots --repo ... | grep <дата>`
2. Восстановить dump нужной БД (payment, persona, learning)
3. Поднять локальный PG, восстановить, сделать SELECT
**RPO:** — | **RTO:** ~1 час | **Глубина:** 12 месяцев

### СЦ-4: Стресс-тест системы (ежеквартальный)

**Кто:** Дмитрий + агент (Claude)
**Когда:** Раз в квартал, плановая проверка готовности
**Действие:** Запустить `backup-stress-test.sh` — 5 сценариев автоматически
**Ожидаемый результат:** Все 5 проверок PASS, отчёт в `DS-ecosystem-development/backups/`

### СЦ-5: Восстановление tsekh-1 после аппаратного сбоя

**Кто:** Дмитрий
**Когда:** Жёсткий диск умер, нужно поднять сервер заново
**Действие:**
1. Установить NixOS, настроить restic с теми же credentials
2. Скачать `/home/tseren` из B2: `restic restore latest --target /`
3. Neon БД — уже в Neon, доступны немедленно
4. Восстановить systemd timers из iwe-server-config
**RPO:** 24 часа | **RTO:** ~4 часа (включая установку ОС)

---

## Проверка работоспособности

```bash
# Последний снапшот Neon
RESTIC_PASSWORD_FILE=/etc/restic/password \
RESTIC_REPOSITORY=b2:aisystant-neon-backup:neon-dbs \
restic snapshots --last

# Размер последнего снапшота (должен быть > 100 МБ)
restic snapshots --json | jq '.[-1].size'

# Запуск стресс-теста
bash ~/IWE/scripts/backup-stress-test.sh
```

---

## Связи

- Роль: `DP.ROLE.NNN-backup-process` (создать в Ф3)
- Runbook восстановления: `DS-ecosystem-development/.../Runbooks/DP.RUNBOOK.NNN-restore.md`
- Стресс-тестер: `~/IWE/scripts/backup-stress-test.sh`
- Мониторинг: BetterStack heartbeat `restic-backups-neon-dbs`
