---
id: DP.METHOD.145
type: method
domain: PACK-digital-platform
status: draft
summary: "Читать конфиг-файл (YAML/JSON) один раз в параллельные bash-массивы; последующие lookup — чистый bash без subprocess. Устраняет N×fork overhead при многократном обращении к одному файлу."
created: 2026-06-26
valid_from: 2026-06-26
version: v1.0
source: "session-transcript 2026-06-26, git diff IWE scripts/day-open-scaffold.sh d4fe987, WP-445 Ф2"
related:
  see_also: [DP.METHOD.059]
---

# DP.METHOD.145: Parse-once shell config

## Назначение

Shell-скрипт, обращающийся к YAML/JSON/INI-конфигу N раз, должен читать файл **один раз** и кешировать в bash-массивы. Последующие lookup — без subprocess.

## Проблема

Каждый вызов вспомогательной функции `read_yaml key` = `fork + python3 + yaml.safe_load` = subprocess overhead.
При N=30 вызовах (типичный `day-open-scaffold.sh`) это:
- N×fork latency
- N парсингов одного файла
- Потенциальный race-condition (файл может измениться между вызовами)

## Паттерн

```bash
# 1. Parse-once: один вызов python3, stdout = ключ\x01значение\nключ\x01значение\n...
_parse_yaml_flat() {
  python3 - "$1" <<'PYEOF'
import sys, yaml

def flatten(d, prefix=''):
    for k, v in d.items():
        key = f"{prefix}{k}"
        if isinstance(v, dict):
            yield from flatten(v, f"{key}.")
        else:
            yield key, str(v) if v is not None else ''

with open(sys.argv[1]) as f:
    data = yaml.safe_load(f)
for k, v in flatten(data):
    print(f"{k}\x01{v}")
PYEOF
}

# 2. Загрузить в параллельные массивы (один раз)
_YAML_KEYS=() _YAML_VALS=()
while IFS=$'\x01' read -r k v; do
  _YAML_KEYS+=("$k")
  _YAML_VALS+=("$v")
done < <(_parse_yaml_flat "$CONFIG_FILE")

# 3. Lookup — чистый bash, без fork
read_yaml() {
  local key="$1"
  for i in "${!_YAML_KEYS[@]}"; do
    [[ "${_YAML_KEYS[$i]}" == "$key" ]] && echo "${_YAML_VALS[$i]}" && return
  done
}
```

## Когда применять

**Тест:** «Функция `read_X()` читает файл при каждом вызове?»
- Да → применить parse-once + array-lookup
- Нет (уже кеширует) → не нужно

**Порог:** ≥3 обращений к одному файлу в скрипте. При 1-2 — накладные расходы на кеш могут превышать выгоду.

## Не применять

- Одноразовый скрипт с 1-2 обращениями к конфигу
- Конфиг должен перечитываться в реальном времени (rare, streaming случай)

## Связи

- DP.METHOD.059 (bash + python3 heredoc portability) — смежный паттерн передачи кода python3 в bash
- P9 engineering-code-style-base.md (не парсить стандартный формат вручную) — данный метод использует библиотечный yaml.safe_load, не ручной парсинг
