---
id: DP.M.040
name: Progress Counter N/M для batch-операций CLI (CLI Batch Progress UX)
type: method
status: draft
valid_from: 2026-05-13
summary: "Вывод прогресс-строки (N/M) в теле batch-цикла в shell-скриптах предотвращает иллюзию зависания при длинных операциях. Порог: >10 итераций или >5 сек."
tags: [cli, ux, shell, batch, progress, template]
source: "git diff FMT-exocortex-template commit cba5c3a, update.sh Step 2, WP-7"
created: 2026-05-13
---

# DP.M.040 — Progress Counter N/M для batch-операций CLI

## Суть метода

Длинный batch-цикл (скачивание N файлов, проверка N детекторов, ~30 сек) без вывода создаёт **иллюзию зависания**: пользователь не знает, выполняется ли процесс или завис.

Решение: вывод прогресс-строки `(N/M) описание...` в теле цикла.

## Реализация (shell)

```bash
TOTAL=${#FILES[@]}
CURRENT=0

for file in "${FILES[@]}"; do
    CURRENT=$((CURRENT + 1))
    printf "(%d/%d) downloading %s...\n" "$CURRENT" "$TOTAL" "$file"
    # ... операция ...
done
printf "\n"
```

## Когда применять

**Порог:** >10 итераций ИЛИ >5 сек суммарного времени.

| Операция | Применять? |
|----------|-----------|
| Скачивание N файлов (~30 сек) | ✅ |
| Проверка N детекторов в validator.sh | ✅ |
| Создание 2-3 файлов | ❌ — нет смысла |
| pip install (уже есть встроенный прогресс) | ❌ — дублирование |

## Альтернативы по контексту

| Контекст | Метод |
|---------|-------|
| Python | tqdm |
| Long-running async | asyncio.gather с callback |
| Многострочный вывод | Rich progress bar |
| Shell, простота важна | N/M counter (этот метод) |

## Применимо в IWE

- `update.sh` Step 2 — скачивание файлов шаблона
- `integration-contract-validator.sh` — прогон детекторов
- любой shell-скрипт с batch-итерацией в FMT или DS
