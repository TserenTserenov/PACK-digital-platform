---
id: DP.M.268
title: "Auto-generated artifact ownership marker pattern"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-02
source: WP-377 Day Open Mode A, commit 395b481e DS-my-strategy (INCIDENT-scheduler-cron-not-fired-2026-06-02.md)
last_updated: 2026-08-01
---

# DP.M.268 — Auto-generated artifact ownership marker

## Описание

Паттерн проектирования для scaffold-систем, автоматически создающих артефакты. Обеспечивает идемпотентную регенерацию без уничтожения ручных правок.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Автоматическая перегенерация ↔ сохранение ручных правок | Scaffold должен обновлять stale файлы, но не должен уничтожать ручной контекст; marker разделяет эти случаи, но добавляет convention, которую нужно соблюдать |
| Idempotence ↔ ownership | Полная идемпотентность удобна для автоматизации, но не учитывает, что пользователь взял файл в работу; marker снимает идемпотентность при ownership |
| Простота логики ↔ точность | Два поля (`auto_generated`, `status`) — простая логика, но не ловит все способы ownership (например, правка без смены status) |

## Проблема

Scaffold создаёт файл при каждом запуске (идемпотентность = «создать, если нет; перегенерировать, если устарел»). При повторном запуске перезаписывает файл → уничтожает ручной контекст, добавленный пользователем (решение, workaround, notes).

## Решение

**Ownership marker** в frontmatter создаваемого файла:

```yaml
auto_generated: true
status: pending
```

**Логика перезаписи:**
```bash
if grep -q "auto_generated: true" "$FILE" && grep -q "status: pending" "$FILE"; then
    # Файл ещё не тронут — можно перезаписать
    regenerate "$FILE"
else
    # Пользователь взял ownership — НЕ перезаписывать
    echo "Skipping $FILE: manual ownership detected"
fi
```

**Пользователь сигнализирует ownership** одним из способов:
- Удалить поле `auto_generated`
- Изменить `status` на `deferred`, `in_progress`, `resolved`, `investigating`

## Ключевые инварианты

- Marker живёт **в самом файле** (не в отдельном реестре) — drift невозможен
- Два состояния: `auto_generated: true` + `status: pending` = «нетронутый»; любое отличие = «взято ownership»
- Idempotent **только для нетронутых файлов**

## Применимость

- Инцидент-файлы, создаваемые Day Open scaffold
- Task-файлы из agent dispatcher
- Report-шаблоны из session-close scaffold
- Любой механизм «create-if-absent + regenerate-if-stale»

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Удаление `auto_generated` забывается | Пользователь внёс правки, но не удалил marker, и scaffold перезаписывает его работу при следующем запуске |
| Слишком много статусов для «нетронутого» | Склонность добавлять статусы вроде `draft` или `review`, что размывает границу ownership и позволяет перезаписать частично взятый файл |
| Игнорирование marker как «технической детали» | Практикующий считает marker несущественным, но именно он определяет, перезапишется ли файл, и защищает ручной контекст |

## Связи

- Реализация: `DS-my-strategy/inbox/INCIDENT-*.md` frontmatter convention
- Смежный: DP.M.267 (grep-marker auto-registry для deferred decisions)
