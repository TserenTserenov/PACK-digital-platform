---
id: DP.M.197
name: "Fix Contract (FC) — исполняемая спецификация исправления с regression_checks"
type: method
pack: PACK-digital-platform
domain: software-quality
trust: 0.85
epistemic_stage: confirmed
valid_from: 2026-05-28
source: WP-358 Ф8 peer-session 2026-05-28-05, commit 9426c5d8 (FC-001.yaml + DLQ regression checks)
---

# DP.M.197 — Fix Contract (FC) — исполняемая спецификация исправления с regression_checks

## Описание

Метод документирования исправления дефекта через исполняемый контракт `FC-NNN.yaml`, содержащий три секции:

1. **`fixes`** — что именно изменили (файлы, функции, SQL-миграции).
2. **`smoke_tests`** — проверки что починенное работает (bash-команды, curl, SQL-запросы).
3. **`regression_checks`** — проверки что старые дефекты не вернулись (deterministic bash-команды).

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Нарративная свобода истории исправлений (CHANGELOG) ↔ исполняемость спецификации | FC платит формальностью трёх секций yaml (fixes / smoke_tests / regression_checks) за то, что проверки можно запускать в CI и deploy pipeline — текст перестаёт быть прозой для людей и становится входом для автомата |
| Стоимость ведения контракта при каждом фиксе ↔ защита от рекуррентных регрессий | Правило «обновлять FC-файл в том же коммите, что и исправление» удлиняет каждый hotfix — метод покупает гарантию, что regression_checks не устареют вместе с кодом, ценой дисциплины, которую CHANGELOG не требует |

## Алгоритм

```yaml
# FC-001.yaml — пример
id: FC-001
date: 2026-05-28
issue: "Turn-taking dispatcher пропускает второй pilot-ход"

fixes:
  - file: dispatcher.py
    change: "Замена числового guard на order-based: turns[-1].role != 'pilot'"
  - file: dlq_helpers.py
    change: "Добавлен insert_dlq() для circuit breaker"

smoke_tests:
  - name: "Dispatcher отвечает на второй pilot-turn"
    cmd: "python test_dispatcher.py --scenario two-pilot-turns"

regression_checks:
  - name: "DLQ table существует в learning DB"
    cmd: "psql $LEARNING_DB -c \"SELECT 1 FROM dlq LIMIT 1\" 2>/dev/null && echo OK"
  - name: "DLQ-функции присутствуют в projection-worker"
    cmd: "grep -q 'def insert_dlq' dlq_helpers.py && echo OK"
```

**Правило обновления:** при добавлении нового исправления к тому же FC — обновлять FC-файл в том же коммите что и исправление.

## Отличие от CHANGELOG

| CHANGELOG | Fix Contract |
|-----------|-------------|
| Нарратив («мы добавили DLQ») | Исполняемые команды (bash/SQL) |
| Описывает историю | Верифицирует состояние |
| Не автоматизируется | Запускается CI/deploy pipeline |
| Устаревает с кодом | Обновляется вместе с кодом |

## Применимость

- Проекты с рекуррентными регрессиями (одни и те же баги возвращаются).
- CI pipeline: FC запускается как дополнительный gate перед деплоем.
- Peer-сессии: FC служит общим языком между агентами («что именно починили»).
- Hotfix workflow: FC создаётся одновременно с PR, не после.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Секция `smoke_tests` («починенное работает») затмевает `regression_checks` («старое не вернулось») | Внимание естественно приковано к свежему исправлению, и проверки прошлых дефектов (DLQ table, insert_dlq) добавляются «потом» или копируются неглядя — FC тихо деградирует в CHANGELOG с командами, теряя своё отличие: верификацию состояния, а не историю |
| Наличие FC-файла читается как его актуальность | Практик смотрит на факт существования контракта в репо и не сверяет его с кодом — правило обновления «в том же коммите» не проверяется, и рассинхрон FC и кода обнаруживается только когда регрессия реально вернулась, то есть ровно тогда, когда контракт был нужен |

## Связи

- WP-358 Ф8 — первый прецедент (FC-001.yaml для DLQ circuit breaker)
- DP.M.162 — peer adversarial critique methodology (смежный peer-метод)


---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 5). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
