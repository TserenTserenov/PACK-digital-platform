---
id: DP.M.287
type: method
title: Grace-window для перекрывающихся scheduled jobs с разным временем запуска
trust: confirmed
epistemic_stage: confirmed
domains: [scheduling, watchdog, cron, batch-processing]
source_session: 2026-06-06 session-close (WP-7 BFA Волна 1+2)
source_commit: 8d84bda (day-open-preflight.sh)
valid_from: 2026-06-06
schema_version: 1
last_updated: 2026-08-01
---

# DP.M.287 — Grace-window для перекрывающихся scheduled jobs

## Контекст

Два scheduled job'а с разными временами запуска:
- **Generator** — создаёт файл/запись (00:01 EEST или при catch-up до 06:03)
- **Auditor** — проверяет наличие артефакта (04:00 EEST)

Auditor может сработать ДО Generator на нормальном рабочем дне — не потому что Generator упал, а потому что catch-up ещё не завершился.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Простота проверки «файл существует» ↔ учёт времени catch-up | Жёсткая проверка в 04:00 проста и ловит реальные провалы, но даёт ложные overdue при нормальном catch-up Generator |
| Доверие к алертам ↔ шум | Частые ложные срабатывания размывают доверие; grace window снижает шум, но рискует скрыть реальный failure, если окно слишком широкое |
| Ранний Auditor ↔ поздний Generator | Auditor хочет проверять как можно раньше; Generator может завершаться только к 06:00; grace window согласовывает их расписания |
| Жёсткое SLA ↔ реальная вариативность | Hardcoded «к HH:MM» легко объяснить, но не отражает retry/catch-up логику Generator; grace window добавляет точности, но усложняет контракт |
| Автоматизация ↔ human override | Можно вручную подавлять алерты при catch-up; но это не масштабируется и превращает Auditor в формальность |

## Метод

Ввести **grace window**: период, в течение которого Auditor возвращает статус `pending` вместо `overdue`.

```bash
GRACE_END_HOUR=6  # 06:30 EEST
current_hour=$(date +%H)

if [ "$current_hour" -lt "$GRACE_END_HOUR" ]; then
    echo "status: pending (within grace window)"
    exit 0
fi

# Только за пределами grace window проверяем наличие артефакта
if [ ! -f "$EXPECTED_ARTIFACT" ]; then
    echo "status: overdue"
    exit 1
fi
```

**Размер grace window** = `max(generator_catch_up_deadline, auditor_fire_time) + buffer`.

## Тест применимости

«Может ли Auditor сработать ДО Generator на нормальном рабочем дне?» Да → grace window обязателен.

Дополнительные сигналы:
- Catch-up / retry логика у Generator с неопределённым временем завершения
- Auditor запускается в пределах 2 часов от Generator

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| «Если артефакта нет — значит overdue» | Проверка существования воспринимается как достаточная; нормальный catch-up Generator игнорируется, и Auditor даёт ложный алерт |
| «Hardcoded deadline проще и надёжнее» | Жёсткое время кажется более строгим контрактом; практикующий не хочет добавлять grace-логику, боясь усложнить |
| «Generator должен успеть к своему scheduled time» | Предположение, что scheduled time равняется фактическому завершению; retry/catch-up window рассматривается как исключение, а не норма |
| «Grace window скроет реальный failure» | Сопротивление добавлению окна из-за страха пропустить провал; в результате Auditor генерирует шум и теряет доверие |
| «Catch-up случается только при сбоях» | Нормальная задержка catch-up приравнивается к инциденту; вместо grace window пытаются «починить» Generator, который не сломан |

## Антипаттерн

Hardcode «артефакт должен существовать к HH:MM» без учёта retry/catch-up сценариев → ложные алерты при первом запуске или в дни с задержкой.

## Применимо к

- Day Open pipeline с overnight-auditor
- CI/CD с pre-flight checks
- Watchdog + generator пары в launchd/cron
- Любой «checker + producer» с разными временами старта

## Связано

- WP-7 BFA Волна 1+2 — источник
- DP.FM.137 (asymmetric alert suppression) — смежный класс ошибок мониторинга
