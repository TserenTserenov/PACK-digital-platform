---
id: DP.M.063
name: Triple-deploy + URL-derived basename для tool promotion
name_ru: Triple-deploy + URL-derived basename для tool promotion
name_en: Triple-Deploy with URL-Derived Basename for Tool Promotion
type: method
status: active
summary: "Инструмент, работающий в авторском IWE + FMT-шаблоне (для других пилотов) + DS-репо — требует 3-х синхронизированных копий. Pattern: (1) одна реализация (Python, не bash), (2) три target-локации с симметричными именами, (3) FMT-версия обезличена через `_repo_basename` из git remote URL вместо hardcoded имени. Тест обезличивания: «если установить шаблон в репо с другим именем — скрипт сам подхватит правильное basename?» Да → корректное обезличивание."
created: 2026-05-17
trust:
  F: 3
  G: domain
  R: 0.80
epistemic_stage: emerging
related:
  uses: []
  references: [DP.M.053]
  realized_by: []
tags: [promotion, deploy, fmt, template, basename, url-derived, hook-promote, skill-promote]
wp: WP-324
---

# Triple-Deploy с URL-Derived Basename для Tool Promotion (DP.M.063)

## 1. Контекст

Инструмент IWE должен работать в трёх контекстах одновременно:

| Контекст | Локация | Назначение |
|----------|---------|-------------|
| Авторский | `~/IWE/scripts/` | Source-of-truth, daily-driver автора |
| Шаблон (FMT) | `FMT-exocortex-template/extensions/<name>/scripts/` | Distribution для других пилотов |
| Domain (DS) | `<DS-repo>/scripts/` | Domain-specific usage |

Три копии → drift-риск. Hardcoded paths (`DS-my-strategy`) в шаблонной версии → ломает установку у других пилотов.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Единая реализация (Python, §2.1) ↔ гибкость под specific-контекст | Один файл легче поддерживать без drift, но тройное развёртывание (§1: авторский/шаблон/domain) иногда просит контекст-специфичные пути — это толкает к if-веткам внутри одного файла вместо трёх чистых копий |
| Обезличивание через URL-derived basename (§2.3) ↔ простота hardcoded путей | Hardcoded path пишется быстрее и очевиднее при разработке в авторском репо, но именно он ломает установку у другого пилота (§4 анти-паттерн 1); `_repo_basename` требует доп. кода ради обезличивания, не нужного для авторской копии |
| Симметрия трёх копий (§2.2) ↔ скорость точечного патча одной локации | Когда нужно быстро пофиксить баг в одной из трёх копий, соблазн запушить только туда конкурирует с требованием синхронного commit во все три target'а, явно перечисленные в сообщении коммита |

## 2. Правило: тройной паттерн

1. **Одна реализация** — Python (не bash), один файл, одна логика. Bash deprecated в этом паттерне, т.к. трудно параметризовать пути.
2. **Три target-локации с симметричными именами** — одинаковый basename, одинаковый shebang, одинаковые args. Commit-сообщение явно перечисляет все три target'а.
3. **FMT-версия обезличена через URL-derived basename:**
   ```python
   import subprocess
   def _repo_basename(repo_path: str) -> str:
       url = subprocess.check_output(["git", "remote", "get-url", "origin"], cwd=repo_path).decode().strip()
       return url.split("/")[-1].removesuffix(".git")
   ```
   ИЛИ env-var override как fallback (`IWE_GOVERNANCE_REPO`).

## 3. Тест обезличивания

«Если установить шаблон в репо с другим именем (`DS-his-strategy`, `governance-foo`) — скрипт сам подхватит правильное basename?»

- **Да** → корректное обезличивание (`_repo_basename` derives at runtime)
- **Нет** → hardcoded path; смотреть на регрессию через grep `grep -r "DS-my-strategy" FMT-exocortex-template/`

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `epistemic_stage: emerging`: пометка `tentative` по прецеденту WP-448 Ф12._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Тест обезличивания откладывается до момента, когда поздно | При разработке инструмента в авторском репо (§1) внимание концентрируется на том, что работает «у меня», и не доходит до вопроса §3 «если установить в репо с другим именем» — тест обезличивания откладывается на момент, когда FMT-версия уже написана с hardcoded путём |
| _(tentative)_ Один параметризованный вызов переоценивается как полное обезличивание | После написания `_repo_basename` (§2.3) внимание переоценивает степень обезличивания всего файла целиком — один явно параметризованный вызов создаёт ощущение «весь скрипт обезличен», хотя рядом могут остаться другие hardcoded строки (например дефолт `IWE_GOVERNANCE_REPO` в примере §7), которые не попали под тот же тест |

## 4. Анти-паттерн

| Анти-паттерн | Симптом | Лечение |
|--------------|---------|---------|
| **Hardcoded path в FMT-версии** | У другого пилота скрипт ищет `DS-my-strategy`, которой у него нет | §2.3 — URL-derived basename + env override |
| **Bash вместо Python** | Невозможно чисто параметризовать пути через args | §2.1 — Python |
| **Promote-механизм не вычищает hardcoded** | Авторская версия копируется as-is, ломает FMT | Авторская версия ДОЛЖНА быть параметризована заранее (не `~/IWE/...`, а `$(_repo_basename ...)`) |
| **Скрипт работает только в одной локации** | Через 2 недели нужна копия для другого пилота — нет процесса | §2 — заранее проектировать tri-deployment |

## 5. Применимость

- **Hook promotion** (`hook-promote.sh`) — три копии хука
- **Skill promotion** (`skill-promote.sh`) — три копии скилла
- **Script promotion** (`script-promote.sh`) — общий механизм
- **Любой ОДИН артефакт-в-N-локациях с обезличиванием** — config-файл, dispatcher, validator

## 6. Связь с другими методами/правилами

- **CLAUDE.md §9 «Extensions Gate / Flow»** — описывает 3-слойный flow (авторский → FMT → пользователи); этот method конкретизирует tooling-сторону flow
- **DP.M.053 (pack-sot-code-mirror)** — близкий принцип: «SoT + N зеркал». Этот метод применяет тот же принцип к tooling, не к pack-знанию
- **`memory/lessons_setup_update_drift.md`** — парные правки setup.sh/update.sh; этот метод предотвращает аналогичный drift через единую реализацию

## 7. Пример (WP-324 Ф8, 17 мая 2026)

Commit `1251f928`: iwe-agent-dispatcher.py развёрнут в трёх локациях:

```
- ~/IWE/scripts/iwe-agent-dispatcher.py             # авторская
- DS-autonomous-agents/scripts/iwe-agent-dispatcher.py  # domain
- FMT-exocortex-template/extensions/agent-inbox/scripts/iwe-agent-dispatcher.py  # template (обезличена)
```

В FMT-версии:
```python
GOVERNANCE_REPO = os.environ.get("IWE_GOVERNANCE_REPO") or _repo_basename(os.path.expanduser("~/IWE/DS-strategy"))
```
Если у другого пилота репо называется `DS-her-governance` — `_repo_basename` подхватит правильно.

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
