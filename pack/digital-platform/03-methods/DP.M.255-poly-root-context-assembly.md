---
id: DP.M.255
name: "Поликорневая сборка контекста"
name_en: "Poly-root Context Assembly"
type: method
status: active
domain: digital-platform
pack: PACK-digital-platform
trust: designed
epistemic_stage: formalized
valid_from: 2026-06-01
related:
  realizes: [DP.SC.164]
  downstream: [DP.M.241]
  see_also: [DP.SC.164]
tags: [poly-context, context-assembly, multi-program, qualification, composition]
source: "WP-379 Ф5 (peer-session 2026-06-01-12)"
schema_version: 1
---

# DP.M.255 «Поликорневая сборка контекста» / «Poly-root Context Assembly»

> **Статус:** перенесено из draft `sessions/2026-06/2026-06-01-09-pack-extraction-poly-root/report.md` в Pack (peer-session 2026-06-01-12).

Метод реализует контекстную составляющую `DP.SC.164 «Доставка персонального руководства пилоту»` §Этап-1 (Сборщик контекста). Собирает фрагменты контекста из нескольких программ развития, упорядоченных по квалификации пилота.

---

## §1. Abstract program model

### qualification_map

```
type:    qualification_id → [program_id+]
where:   [program_id+] = ordered sequence (array / list), not set
semantics: order of elements = priority order (index 0 = highest priority)
source:  provided by DS implementation
```

`qualification_map` — это mapping, предоставляемый DS-реализацией. Определяет, какие программы применимы для данной квалификации, и в каком порядке.

### context_resolver

```
type:    program_id → context_block
where:   context_block: string
errors:  handled implementation-specific (not defined by this contract)
source:  provided by DS implementation
```

`context_resolver` — функция (или callable), принимающая `program_id` и возвращающая `context_block`. Детали реализации (источник данных, кэширование, формат) определяются DS и не нормируются этим методом.

---

## §2. Output contract

### poly_context

```yaml
poly_context: [{program_id: string, context_block: string, ...}]
```

- `...` = additional fields permitted but unspecified by this contract
- `context_block` MUST satisfy:
  - **non-empty:** not `None`, not empty string
- `design_note: [informative]` `context_block` intended to characterize program applicability to support downstream LLM routing decision

### program_tag

```yaml
program_tag: string
```

- **construction:** `"+".join(program_ids_in_qualification_order)`
  - where: `program_ids_in_qualification_order = qualification_map[qualification_id]`
  - note: order is preserved from `qualification_map`, not sorted or reordered
- **derived:** `true`
- **usage:** annotation-only
- **invariant:** downstream consumers MUST NOT use `program_tag` as routing key
  - where "routing key" = branch selector for execution logic
  - **prohibited usage:**
    - `dispatch[program_tag] → handler`
    - `if program_tag == "X" then render_path_A() else render_path_B()`
  - **permitted usage:**
    - logging: audit trail, debugging
    - frontmatter annotation: metadata for human readers
    - display: UI label, report header
    - cross-reference: linking to program catalog

---

## §3. Context Assembly Algorithm

### Preconditions (caller's obligation)

- `qualification_id` is not `None` and is validly formed

### Documented error conditions (method's obligation to signal)

- `qualification_id` not found in `qualification_map` →
  - implementation **MUST** return explicit error signal
  - (MAY raise OR return error-result; MUST include `qualification_id` in signal)

- `context_resolver(program_id)` failure →
  - implementation **MUST** return explicit error signal
  - (MAY raise OR return error-result; MUST include `program_id` in signal)
  - partial `poly_context` **MUST NOT** be returned as successful output

### Design notes

- `[informative]` downstream consumer assumed to be LLM inference layer
- `[informative]` pure-code consumers MUST add explicit deduplication at integration point

### Input

```
qualification_id + implementation-specific context_resolver
```

### Output

```
poly_context as defined in §2, program_tag as defined in §2
```

### Algorithm

1. Resolve applicable programs via `qualification_map`
2. For each program: resolve `context_block` via `context_resolver`
3. **Merge:** order-preserving concatenation (qualification order = priority)
4. **No deduplication** at block level (delegated to downstream consumer)
5. Construct `program_tag` per §2 construction rule

### Invariants

- **order-preserving:** qualification order = priority order
- **additive:** `context_block`s are concatenated, not merged or resolved

---

## §4. Связи

```yaml
related:
  realizes: [DP.SC.164]
  downstream: [DP.M.241]
  see_also: [DP.SC.164]
```

- **DP.SC.164** — обещание, часть которого реализует этот метод (§Этап-1: Сборщик контекста)
- **DP.M.241** — downstream consumer: §S4 потребляет `poly_context` из §2 данного метода

---

## §5. Иллюстративные примеры (informative)

> Примеры не нормативны. Иллюстрируют §2 output contract и §3 algorithm.

### Сценарий 1 (single-program, дегенеративный)

- `qualification_id = "student"`
- `qualification_map["student"] = [PD]`
- `context_resolver("PD")` → `"..."`
- Result:
  ```yaml
  poly_context:
    - {program_id: "PD", context_block: "..."}
  program_tag: "PD"
  ```

### Сценарий 2 (multi-program)

- `qualification_id = "knowledge-worker"`
- `qualification_map["knowledge-worker"] = [PD, WD]` (qualification order = priority order)
- `context_resolver("PD")` → `"..."`
- `context_resolver("WD")` → `"..."`
- Result:
  ```yaml
  poly_context:
    - {program_id: "PD", context_block: "..."}
    - {program_id: "WD", context_block: "..."}
  program_tag: "PD+WD"
  ```
- Порядок в `program_tag` = порядок элементов в `qualification_map` (PD первый, WD второй).

---

## Источники решений и связки

- **Peer-сессия 2026-06-01-09** (структура §1–§3): `DS-my-strategy/sessions/2026-06/2026-06-01-09-pack-extraction-poly-root/report.md`
- **Peer-сессия 2026-06-01-12** (финализация): `DS-my-strategy/sessions/2026-06/2026-06-01-12-pack-extraction-f5-finalize/report.md`
- **DP.SC.164** — обещание, которое реализует этот метод
- **DP.M.241** — downstream consumer
- **WP-379** — рабочий продукт извлечения метода из working code
