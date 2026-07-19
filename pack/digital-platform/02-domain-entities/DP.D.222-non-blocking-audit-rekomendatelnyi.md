---
id: DP.D.222
name: "Non-blocking аудит (рекомендательный канал) ≠ Слой защиты (blocking-by-default)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-07-10
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.222: Non-blocking аудит (рекомендательный канал) ≠ Слой защиты (blocking-by-default)

| Non-blocking аудит | Слой защиты |
|-------------------|-------------|
| Не блокирует merge по умолчанию | Блокирует merge по умолчанию |
| Ревьюер может игнорировать под давлением дедлайна | Ревьюер не может аппрувнуть без fix |
| Обход попадает в main, живёт до следующего полного аудита | Обход отклоняется при merge |
| «Обязателен для review» = процессная декларация | Blocking-статус = технический инвариант |
| Week-Close аудит — слишком редко для критичного контура | Запуск на каждый push в main |

**Почему важно:** Процессные декларации («должен быть проверен») имеют нулевую силу под давлением дедлайна или при direct push. Настоящий слой защиты: (1) blocking-by-default для PR, затрагивающих файлы запуска; (2) ложные срабатывания решаются audited whitelist + documented break-glass, а не ослаблением гарда; (3) запуск на каждый push в main, иначе direct push и post-merge drift проходят мимо.

**Тест:** «Может ли ревьюер аппрувнуть этот PR без фикса?» Да → это рекомендация, не слой защиты.

**Применение:** при проектировании security-контуров различать, является ли проверка блокирующим шлюзом или рекомендательным каналом. Non-blocking = advisory. Для enforcement нужен blocking + break-glass + out-of-band trigger (push в main).

**Связано с:** DP.D.144 (детектор существует ≠ детектор блокирует), DP.FM.237 (Provenance вне репо), DP.FM.238 (Self-referential exemption), DP.METHOD.154 (bypass-class taxonomy).

**Источник:** peer-session 2026-06-26-26-wp436-claude-writer, turn 2; WP-436.
