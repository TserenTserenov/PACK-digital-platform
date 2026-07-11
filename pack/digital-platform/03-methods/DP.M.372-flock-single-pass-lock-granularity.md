---
id: DP.M.372
name: "flock: один общий лок на весь проход vs per-item при последовательной внутренней обработке"
type: method
pack: PACK-digital-platform
domain: digital-platform / concurrent-scripts
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-close 2026-07-10, iwe-server-config commit 1055eb8 (tsekh-timer-race fix)"
see_also: [DP.FM.228, DP.FM.229]
schema_version: 1
---

# DP.M.372 — flock: уровень гранулярности блокировки ≡ уровень реального параллелизма

**Суть:** Уровень блокировки выбирается по уровню реального параллелизма, а не по количеству ресурсов. Если конкурирующие процессы обрабатывают ресурсы последовательно внутри себя — один общий лок на весь проход корректен и проще per-item лока.

## Принцип выбора гранулярности

| Структура конкурентов | Правильный уровень блокировки |
|----------------------|-------------------------------|
| Оба скрипта итерируют ресурсы последовательно | Один общий лок на весь проход |
| Скрипты параллельны внутри (goroutine/thread per item) | Per-item блокировка |
| Смешанный случай | Лок на «проход группы», не на один item |

## Паттерн

```bash
LOCK_FILE="/tmp/iwe-git-sync.lock"

(
  flock -n 200 || { echo "Already running, exiting"; exit 0; }
  # Весь цикл по репозиториям — под одним локом
  for repo in "${REPOS[@]}"; do
    git -C "$repo" pull --rebase
  done
) 200>"$LOCK_FILE"
```

## Антипаттерн

```bash
# ИЗБЫТОЧНО: per-repo лок при последовательном цикле
for repo in "${REPOS[@]}"; do
  flock "/tmp/iwe-${repo##*/}.lock" git -C "$repo" pull --rebase
done
# Не предотвращает race на FETCH_HEAD — два скрипта могут зайти
# в разные репо одновременно и конкурировать там
```

## Применимо

Любые два скрипта, обходящих общий набор ресурсов в цикле последовательно. Инфраструктурные cron/systemd-скрипты, batch-операции с файловой системой, multi-repo git-операции.

## Связано

- DP.FM.228: FETCH_HEAD race — конкретный инцидент, потребовавший этот паттерн
- DP.FM.229: OnBootSec gap collapse — почему таймеры схлопываются к одному моменту
