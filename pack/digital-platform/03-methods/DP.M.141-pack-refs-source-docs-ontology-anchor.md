---
id: DP.M.141
name: "Выбор source в pack_refs: ID Pack vs docs + ontology_anchor"
type: method
trust: validated
epistemic_stage: confirmed
valid_from: 2026-05-21
domains: [guide-authoring, pack, traceability]
---

# DP.M.141 — Выбор source в pack_refs: ID Pack vs docs + ontology_anchor

## Два режима source в pack_refs

В поле `pack_refs[*]` существует два режима `source` в зависимости от того, прошёл ли концепт полную формализацию:

| Режим | Условие | Пример |
|-------|---------|--------|
| `source: <ID>` | Концепт формализован: существует файл `PACK-*/pack/**/<ID>-*.md` | `source: PD.FORM.127` |
| `source: docs` + `ontology_anchor` | Концепт есть в `ontology.md`, но нет отдельного Pack-файла (status: forming / formalized без выделенной сущности) | `source: docs`, `ontology_anchor: ../../../PACK-personal/ontology.md#профилактика-дрейфа` |

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Полная трассируемость к Pack-файлу ↔ стоимость формализации каждого концепта | Режим `source: docs` + `ontology_anchor` позволяет ссылаться на концепт, не создавая отдельный Pack-файл, но цена — ссылка на anchor в `ontology.md` вместо стабильного ID: меньше формализации сейчас, больше хрупкости ссылки потом |
| Краткость записи `source: docs` ↔ верифицируемость ссылки | Одно поле короче, но без `ontology_anchor` трассируемость битая (failure mode карточки): метод требует пары полей и grep-теста, который должен вернуть пустой результат, — дисциплина записи вместо экономии одной строки frontmatter |

## Алгоритм выбора

```
Есть файл PACK-*/pack/**/<ID>-*.md?
├── Да → source: <ID>
└── Нет, но есть запись в ontology.md?
    ├── Да → source: docs, ontology_anchor: <путь>#<anchor>
    └── Нет → failure mode (концепт не трассируем)
```

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| `source: docs` как дефолтная ветка вместо алгоритма выбора | Внимание практика при заполнении `pack_refs` тянется к более простой записи `source: docs`, и первый вопрос алгоритма — «есть ли файл `PACK-*/pack/**/<ID>-*.md`?» — не задаётся: концепт, уже имеющий стабильный Pack ID, получает хрупкую docs-ссылку |
| Факт записи anchor затмевает его живучесть | Внимание сосредотачивается на том, что `ontology_anchor` заполнен и grep-тест проходит в момент коммита, а устойчивость ссылки (переименование заголовка в `ontology.md` молча ломает anchor) остаётся вне поля зрения — верифицируемость подтверждена один раз и не перепроверяется |

## Failure mode: source: docs без ontology_anchor

Указать `source: docs` без `ontology_anchor` → битая трассируемость: anchor недоступен для верификации.

**Тест:** `grep -l "source: docs" *.md | xargs grep -L "ontology_anchor"` → должен быть пустым.

## Применимо к

SS-файлам персональных руководств при добавлении `pack_refs` для концептов, не прошедших полную формализацию как отдельный Pack-файл. Применяется к агентам, пишущим SS-файлы.

---

> 2026-07-31 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 4). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
