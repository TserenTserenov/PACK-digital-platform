---
id: DP.M.154
name: Embedded Python в bash — обязательные with-блоки (CPython-refcount-independence)
type: method
status: draft
valid_from: 2026-05-22
summary: "Embedded-Python сниппет в shell-скрипте для write-операций над manifest/config/state-файлами обязан использовать `with open(...) as f:` для каждого open. Безсонтекстный `json.dump(d, open(f, 'w'))` зависит от CPython refcount-driven __del__ — рискует partial-write на async/PyPy/exception."
related:
  prevents: [DP.FM.005]
  see_also: [DP.M.101]
tags: [python, bash, embedded, file-io, release-process, idiom]
source: "FMT-exocortex-template commit 0fe32f5 — fix #2 по Kimi-ревью RELEASE-PROCESS.md (2026-05-22)"
schema_version: 1
---

# DP.M.154 — Embedded Python в bash: with-блоки обязательны

## Суть метода

В shell-скриптах часто встраивают однострочный Python для read/write над manifest/config/state-файлами:

```bash
python3 -c "import json; json.dump(d, open('manifest.json', 'w'))"
```

**Антипаттерн.** Запись через `open(...)` без `with` полагается на CPython refcount-driven `__del__` для закрытия и flush'а файла. На CPython обычно работает, но:

1. В **async-контексте** файл может закрыться после следующего step'а — race на чтение.
2. На **PyPy/Jython** garbage collection deferred → запись может потеряться.
3. При **exception** между `open()` и `dump()` файл остаётся открытым с partial data.
4. **Default encoding** у `python3` зависит от locale — без явного `encoding='utf-8'` возможны кросс-платформенные дефекты (Windows/macOS/Docker).

**Корректный паттерн** (heredoc с `with` и явным encoding):

```bash
python3 <<'PY'
import json
with open('manifest.json', 'w', encoding='utf-8') as f:
    json.dump(d, f, ensure_ascii=False, indent=2)
PY
```

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Краткость однострочника ↔ детерминированное закрытие файла | `python3 -c "json.dump(d, open(f, 'w'))"` короче heredoc, но отдаёт момент flush и close на откуп refcount-driven `__del__` — with-блок платит объёмом сниппета за гарантированное закрытие при любом сценарии выхода, включая exception |
| Работоспособность на CPython ↔ переносимость на другие интерпретаторы и платформы | На CPython запись без `with` обычно «работает», но цена проявляется в async-контексте, на PyPy/Jython и при падении между `open()` и `dump()`; явный `encoding='utf-8'` добавляет ещё строку ради инварианта вне зависимости от locale |

## Когда применять

- Release-скрипты, где данные критичны (manifest version bump, state-файлы, конфиги deployment).
- Любые embedded-Python в bash с write-операциями.
- Скрипты, которые могут быть запущены под PyPy/альтернативным интерпретатором.

## Когда НЕ применять

- Однократные ad-hoc read-операции на CPython (`python3 -c "print(json.load(open('f.json'))['x'])"`) — read не страдает refcount-проблемой так же сильно.

## Тест применимости

«Что произойдёт, если процесс упадёт между `open(...)` и `dump(...)`?» Корректный ответ: «файл закрыт без partial data» (with-блок). Иначе — нужен with.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Успешный прогон на CPython затмевает условия отказа | «У меня записалось» читается как доказательство корректности, а дефект проявляется только при падении процесса, на PyPy или в async-контексте — сценарии, которые практик не воспроизводит, остаются вне поля зрения до прода |
| Содержимое записи затмевает момент закрытия | Внимание верификации направлено на JSON в файле, а не на то, когда именно файл был закрыт и сброшен — race между записью и чтением следующим шагом release-скрипта остаётся невидимым при точечной проверке результата |

## Ссылки

- Источник: FMT-exocortex-template commit 0fe32f5 (RELEASE-PROCESS.md, fix #2 по Kimi-ревью)
- Предотвращает: DP.FM.005 (Model-Reality Drift) — partial-write манифеста создаёт несогласованность между задокументированным состоянием и реальным

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 5). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
