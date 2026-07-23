---
id: DP.FM.346
name: "wp-sync-bundle.sh слепой к markdown-зачёркиванию: done-строка выглядит как pending"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-07-11
created: 2026-07-22
source: "session-transcript 2026-07-11 + git diff DS-my-strategy (39bd8d8c7, fix wp251 ложный блокер)"
related:
  references: [DP.FM.018]
  see_also: [AR.272]
tags: [wp-sync-bundle, markdown, strikethrough, parsing, status-detection, registry]
---

# DP.FM.346 — wp-sync-bundle.sh слепой к markdown-зачёркиванию

## Паттерн

Парсер WP-реестра (`wp-sync-bundle.sh` или аналог) читает `docs/WP-REGISTRY.md` построчно без учёта markdown-форматирования. Строки вида `| ~~228~~ | ~~Название~~ | ✅ | done |` — семантически «завершено» — парсером не распознаются как done; скрипт берёт значение поля `status:` из frontmatter архивного файла, которое может быть stale `pending`.

## Пример

```
# В реестре:
| ~~228~~ | ~~WP-228 Название~~ | ✅ | done |

# wp-sync-bundle.sh видит строку с "228", не различает ~~ → читает frontmatter:
# archive/wp-contexts/WP-228.md → status: pending (stale)
# Возвращает: WP-228 = pending (ЛОЖНЫЙ статус)
```

## Механизм

1. Реестр использует markdown-зачёркивание (`~~NNN~~`) как конвенцию "done"
2. Парсер ищет число в строке без фильтрации `~~` → находит строку done-РП
3. Fallback на frontmatter архивного файла, который не обновлялся после закрытия
4. Результат: done-РП выглядит как pending-блокер

## Почему опасен

- Логика «блокер стоит → РП нельзя открыть» ломается: блокер закрыт, но парсер этого не видит
- Ложный блокер может простоять месяцами (в реальном кейсе — с апреля по июль)
- Нет явной ошибки — скрипт возвращает значение, которое выглядит правдоподобно

## Лечение

1. При grep строки реестра по номеру РП — явно фильтровать `~~N~~` как done: `grep "| ~~${WP_NUM}~~"` → done; `grep "| ${WP_NUM} "` → active
2. Не брать frontmatter архивного файла как SoT статуса — только реестр (AR.272)
3. Тест: файл с `~~228~~` → bundle → проверить, что status возвращён как done, не pending

## Связи

- DP.FM.018 (markdown-разметка ломает shell-processing) — родительский класс; этот FM конкретен: семантика статуса в реестре
- AR.272 (реестр > frontmatter для статуса РП) — правило, нарушение которого создаёт этот FM
