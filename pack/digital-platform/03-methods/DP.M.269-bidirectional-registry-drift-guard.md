---
id: DP.M.269
title: "Bidirectional registry drift guard via commit-msg hook"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-02
source: peer-session 2026-06-01-21, DS-my-strategy commits 7090877 + 395b481e, DS-my-strategy CLAUDE.md §WP-REGISTRY drift guard
last_updated: 2026-08-01
---

# DP.M.269 — Bidirectional registry drift guard via commit-msg hook

## Описание

Метод принудительной двусторонней синхронизации между рабочими файлами и их реестром через git commit-msg hook. Drift обнаруживается в момент commit, не при следующем review.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Строгая синхронизация ↔ скорость коммита | Hook блокирует drift, но замедляет коммит и требует обновлять реестр даже для мелких правок статуса |
| Escape hatch ↔ злоупотребление | `[no-registry-touch]` нужен для легитимных исключений, но каждый escape — потенциальный путь обхода guard'а, требующий аудита |
| File-level check ↔ точность | File-level check прост и надёжен для >95% случаев, но не ловит hunk-level изменения; hunk-level точнее, но ломается на markdown-таблицах с pipe в тексте |

## Алгоритм

**Условие применения:** есть пара «реестр (index-файл) ↔ рабочие файлы (detail-files)» где расхождение приводит к stale state.

**Hook проверяет два направления:**
1. **Forward:** изменён detail-файл (e.g. `inbox/WP-NNN.md`) без реестра (`docs/WP-REGISTRY.md`) → commit блокируется.
2. **Backward:** изменён реестр без хотя бы одного detail-файла → commit блокируется.

**Escape hatch:** тег `[no-registry-touch]` в commit message для легитимных исключений:
- Typo/formatting fix без изменения статуса
- Frontmatter tag update без status change
- Subfile в папке `WP-NNN/` при неизменном `WP-NNN.md`
- Structural change в шапке/footer реестра

**Аудит:** Week Close считает использования escape-тега за 7 дней. `>2/неделю` → флаг расследования (incentive обхода Guard'а).

**Периодическая reconciliation:**
- Day Open: `--deep-check` (orphan detection) — informational
- Week Close: `--semantic-check` (status vs placement) — informational, генерирует backlog

## Ключевые свойства

- **Drift обнаруживается в момент commit** — no latency
- **Escape hatch аудитируется** — нельзя злоупотреблять незаметно
- **File-level check** достаточен для >95% случаев (vs hunk-level парсинг, который ломается на markdown-таблицах с pipe в тексте)

## Применимость

Любая пара с обязательной синхронизацией:
- `inbox/WP-*.md` ↔ `WP-REGISTRY.md`
- `routes.yaml` ↔ `handlers/*.py`
- `manifest.json` ↔ `implementations/*.md`

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| «Это just formatting, можно [no-registry-touch]» | Практикующий склонен использовать escape hatch для удобства, даже когда изменение всё-таки затрагивает статус или placement |
| Игнорирование Week Close audit | Escape-теги не проверяются периодически, и злоупотребление накапливается до тех пор, пока guard не теряет смысл |
| «Forward direction важнее backward» | Проверяют только изменение detail-файлов без реестра, но не наоборот, что даёт orphan-записи в реестре |

## Связи

- Реализация: `DS-my-strategy/.git/hooks/commit-msg` + `CLAUDE.md §WP-REGISTRY drift guard`
- Сессия: `sessions/2026-06/2026-06-01-21-wp-registry-drift-guard/report.md`
- Смежный: DP.M.267 (grep-marker auto-registry для deferred decisions)
