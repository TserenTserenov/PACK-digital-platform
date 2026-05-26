---
id: DP.D.101
name: "Shared Module Sharing: Symlink (α) ≠ Submodule (β) ≠ Vendor Copy (γ / γ-prime)"
type: distinction
kind: Distinction
pack: PACK-digital-platform
domain: architecture
trust: high
epistemic_stage: formed
status: active
valid_from: 2026-05-26
sources:
  - "WP-7 TS1.A close — ArchGate decision γ-prime (DS-my-strategy commit 4b0a0123)"
  - "Ф-MCP-JWT-Shared-Module (DS-my-strategy WP-7 inbox)"
---

# DP.D.101 — Shared Module: Symlink ≠ Submodule ≠ Vendor Copy

> Четыре варианта совместного использования общего модуля между N независимыми процессами (launchd plists, CF Workers, daemon-агенты). Выбор зависит от частоты изменений, требуемой атомарности замены, и независимости отказов.

## Различение

| Аспект | (α) Symlink | (β) Git submodule | (γ) Vendor copy | (γ-prime) Vendor + автосинхронизация |
|--------|-------------|-------------------|-----------------|----------------------------------------|
| **Механика** | `ln -s shared/lib.py process-N/lib.py` | submodule в каждом репо | копия файла в каждый процесс | копия + promote-script с drift-чеком |
| **Атомарность замены** | Да (одним touch на все процессы) | Нет (каждый submodule update) | Нет (per-process update) | Нет, но drift-detection ловит расхождение |
| **Независимость отказов** | Низкая (sym-target меняется → ломается всё) | Средняя (checkout submodule может сломать) | Высокая (per-process rollback) | Высокая + контролируемая sync |
| **Fragility при deploy** | Высокая (rsync ломает sym, sandbox блокирует) | Средняя (forget submodule update) | Низкая | Низкая |
| **Overhead per-process** | Минимальный | Высокий (init/update на каждый repo) | Средний (дублирование файлов) | Средний + promote-tool |
| **Простота rollback** | Сложный (revert sym-target глобально) | Сложный (submodule pointer) | Простой (per-process restore) | Простой |

## Когда выбирать

- **α (symlink):** один пользователь, один runtime, общий FS, изменения частые (≥1/день). Анти-паттерн при rsync/deploy/sandbox изоляции.
- **β (submodule):** код-сёрс шарится между разными ребозиториями, нужен явный pointer на версию, изменения редкие.
- **γ (vendor copy):** N независимых процессов, изменения редкие (≤1/месяц), важна независимость отказов, ручной sync приемлем.
- **γ-prime:** γ + изменения чуть чаще (1/неделя–1/месяц), нужна гарантия отсутствия drift'а → promote-script сравнивает копии и предупреждает.

## Тест применимости γ-prime

«Нужна ли одновременная атомарная замена во всех процессах?"
- Да → не γ (нужен α или другой механизм с central state).
- Нет → γ-prime ок.

«Изменения в shared module чаще раза в неделю?»
- Да → γ-prime overhead drift-чека превысит выгоду → нужен α или central-state.
- Нет → γ-prime ок.

## Анти-паттерн γ-prime

Shared module меняется ежедневно (например, общая конфигурация ключей, активные feature-флаги) → drift между копиями превысит выгоду независимости. Использовать central-state (БД, KV-store) или α.

## Связи

- AR.219 — FMT-promotion параметризация (один из механизмов γ-prime sync — promote-script)
- DP.D.080 — Control vs Operation (control-плейн для drift-detection)
- WP-7 TS1.A (первый прецедент γ-prime)
- Ф-MCP-JWT-Shared-Module (второй прецедент γ-prime)
