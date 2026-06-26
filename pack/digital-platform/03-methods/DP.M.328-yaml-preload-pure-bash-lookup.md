---
id: DP.M.328
title: "Preload-once парсинг конфига → pure-bash lookup (устранение N subprocess-вызовов)"
type: method
domain: digital-platform
status: draft
valid_from: 2026-06-25
fpf_parent: U.Method
sources:
  - git diff FMT-exocortex-template 51cb56d (fix(#207) Day Open token reduction)
  - day-open-scaffold.sh (preload YAML once → pure-bash array lookup; SWEEP_WP_FULL compute-once reuse)
related:
  see_also: [DP.M.077, DP.M.046]
tags: [bash, performance, yaml-preload, subprocess-reduction, compute-once, script-design]
---

# DP.M.328 — Preload-once парсинг конфига → pure-bash lookup

## Описание

Скрипт, многократно читающий значения из YAML/JSON-конфига через дорогой внешний
интерпретатор (`python3 -c "import yaml"`, `yq`, `jq`), на каждый вызов `read_yaml()`
порождает отдельный subprocess. При N вызовах = N spawn'ов. Метод: распарсить конфиг
**один раз при старте** в параллельные bash-массивы (`_YAML_KEYS` / `_YAML_VALS`),
последующие lookup'ы — pure-bash цикл по индексам без spawn'ов.

Обобщение: любое дорогое вычисление, переиспользуемое ≥2 раз (вызов CLI, сетевой
запрос, тяжёлый парсинг), вынести в одно вычисление + дешёвое переиспользование.
Частный случай в той же правке — `SWEEP_WP_FULL`: предвычислить один раз, переиспользовать
на втором call-site.

## IPO

| Вход | Обработка | Выход |
|------|-----------|-------|
| Конфиг + ≥3 точек чтения | Один parse при старте → bash-массивы; lookup = индексный цикл | Тот же результат, N→1 subprocess-вызовов |

## Алгоритм

```bash
# Старт: один parse в параллельные массивы
_preload_yaml() {
  local out; out=$(python3 -c 'import yaml,sys
d=yaml.safe_load(open(sys.argv[1]))
[print(f"{k}\t{v}") for k,v in d.items()]' "$1")
  while IFS=$'\t' read -r k v; do _YAML_KEYS+=("$k"); _YAML_VALS+=("$v"); done <<< "$out"
}

# Lookup: pure-bash, без spawn
read_yaml() {
  local i; for i in "${!_YAML_KEYS[@]}"; do
    [[ "${_YAML_KEYS[$i]}" == "$1" ]] && { printf '%s' "${_YAML_VALS[$i]}"; return; }
  done
}
```

## Тест применимости

«Сколько раз скрипт зовёт дорогой инструмент (python/jq/yq/awk) с одним и тем же входом?»
≥3 → применить preload-once. 1-2 → не усложнять.

## Признак нарушения

- `read_yaml()` / `jq` / `yq` внутри цикла или вызываемый на каждое поле
- Профиль скрипта показывает десятки spawn'ов одного интерпретатора за прогон

## Практический пример (Day Open, FMT-exocortex-template 51cb56d)

`day-open-scaffold.sh`: preload YAML один раз вместо ~10 `read_yaml()`-spawn'ов за Day Open;
`SWEEP_WP_FULL` предвычислен один раз и переиспользован на втором call-site.

## Связи

- DP.M.077 — common-prefix-compression (другая ось оптимизации: размер вывода, не число вызовов)
- Различение P2 «DRY на 3-м повторении» (engineering-code-style-base) — про дублирование
  *исходника*; этот метод — про дублирование *рантайм-вызовов* (разные оси)
