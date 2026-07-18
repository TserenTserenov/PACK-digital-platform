---
id: DP.D.251
name: "Service-Layer Permission ≠ Execution-Layer Obligation"
type: distinction
pack: PACK-digital-platform
domain: digital-platform / security / consent
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-11
source: "WP-417 Ф6; DS-my-strategy/inbox/captures.md:13048"
see_also: [DP.D.035]
schema_version: 1
---

# DP.D.251 — Service-Layer Permission ≠ Execution-Layer Obligation

## Различение

- **Service-Layer Permission** — разрешение, существующее в протоколе взаимодействия: пилот сказал «разрешаю», в чате появилась устная договорённость.
- **Execution-Layer Obligation** — фактическое состояние процесса, в котором агент исполняет действие: переменные окружения, config-файлы, runtime-флаги.

## Граница

Разрешение в словах не меняет состояние запущенного процесса. Если guard читает env/config при старте, устная договорённость требует перезапуска или reload конфига, иначе действие останется заблокированным.

## Применение

- Любой guard, читающий состояние при старте процесса, должен игнорировать устные разрешения из истории чата.
- После изменения разрешения пилот должен явно перезапустить/перезагрузить конфиг и повторить разрешение в инфраструктурной форме.

## Источник

WP-417 Ф6; DS-my-strategy/inbox/captures.md:13048.
