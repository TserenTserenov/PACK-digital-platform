---
id: DP.ROLE.052
name: Когнитивный прокси-аналитик
alias: R35
type: role-description
status: draft
valid_from: 2026-05-18
summary: "Извлекает косвенные характеристики (cp.wld, cp.agt, bh.awr) из текстового содержания пилота через внешний memory-провайдер. Пишет ТОЛЬКО в cognitive-схему через scope guard. Не имеет доступа к stage, certificate или детерминированным характеристикам."
related:
  specializes: [U.RoleAssignment]
  component_of: [DP.ROLE.001]
  realizes: [DP.SC.142]
  uses:
    - DP.SC.004      # captures как входной контент
    - DP.SC.020      # w_reflection_submitted события
    - DP.SC.135      # Agent Inbox — пакетные задачи анализа
  downstream_consumers:
    - DP.ROLE.041    # Аттестатор — читает cognitive через proxy_reader
    - DP.ROLE.009    # Портной — читает cognitive через proxy_reader
    - MIM.R.009      # Диагност — читает cognitive через proxy_reader
created: 2026-05-18
updated: 2026-05-18
wp: WP-316
---

# Когнитивный прокси-аналитик — DP.ROLE.052 (R35)

> # see DP.SC.142, DP.ROLE.052
>
> **Kind:** Analyst Role — извлекает характеристики из неструктурированного текста.
> **Owner Role:** IWE Platform — исполнитель: Honcho / Mem0 / Hindsight (выбирается по результатам Ф8.3 WP-316).

---

## 1. Миссия

Заполнить информационный пробел, который детерминированные прокси (cp.rhy/cp.skl/cp.iwe/cp.int из activity_log) не покрывают: **cp.wld (мировоззрение)** и **cp.agt (деятельность)** слабо проявляются в бихевиоральных метриках, но хорошо читаются из рефлексий, captures и переписки.

Аналогия: врач-диагност, у которого есть анализы крови (детерминированные данные) и данные из интервью с пациентом (текстовый анализ). Оба источника независимы, оба вносят вклад в диагноз — но врач не ставит диагноз **только** по интервью.

**Граница:** R35 не имеет доступа к cp.rhy, cp.skl, cp.iwe, cp.int, stage, certificate. Запись в `learning.cognitive` изолирована от `learning` основной схемы. Аттестатор читает результат через `cognitive_proxy_reader` (read-only).

---

## 2. Scope Guard (Вариант А, БЛОКИРУЮЩЕЕ)

```
РАЗРЕШЕНО записывать:          ЗАПРЕЩЕНО записывать:
  cp.wld                         cp.rhy
  cp.agt                         cp.skl
  bh.awr                         cp.iwe
                                  cp.int
                                  stage
                                  certificate
                                  cp.* (все остальные)
```

**Parliament Model:** `cognitive`-схема изолирована. `attestator` и `points_calculator` — no access. Только `cognitive_proxy_reader` — read-only для Аттестатора/Портного/Диагноста.

---

## 3. Обязанности

| Обязанность | Как выполняется |
|-------------|----------------|
| Получить текстовый контент | W-рефлексии из `learning.w_reflections`, captures из DP.SC.004, bot-переписка |
| Очистить PII (scrub) | PII scrubber (качество подтверждено на русском тексте — требование PII Gate) перед отправкой провайдеру |
| Отправить контент провайдеру | Honcho / Mem0 / Hindsight API (выбор по результатам benchmark Ф8.3) |
| Получить инференцию | cp.wld ∈ [0,5], cp.agt ∈ [0,5], bh.awr ∈ [0,5] + confidence score |
| Проверить scope guard | Убедиться что полученные ключи ∈ {cp.wld, cp.agt, bh.awr} — иначе drop |
| Записать в cognitive-схему | `INSERT INTO learning.cognitive (ory_identity, characteristic, value, weight, source, provider, updated_at)` |
| Обновить cp_assessments через merge | Аттестатор объединяет cognitive + activity каналы по правилу w_honcho ∈ [0.1, 0.3] |
| Логировать провенанс | source='text_analysis', provider='<name>', session_id, content_hash |

---

## 4. PII Gate — блокирующие условия

> **До закрытия PII Gate (B7.3) — только синтетические данные.**

| Условие | Статус | Как проверить |
|---------|--------|--------------|
| Регион хранения данных у провайдера | ⬜ | Ответ провайдера на запрос §1 |
| DPA подписан + retention policy ≤30 дней | ⬜ | Документация в DS-ecosystem-development/.../ |
| GDPR Art.17 API (право на удаление) | ⬜ | Тест-запрос через sandbox |
| Scrubber quality ≥90% precision на русском | ⬜ | Benchmark на 100 реальных рефлексий |
| Self-host опция доступна | ⬜ | Ответ провайдера на запрос §5 |

**Алгоритм:** Если хотя бы одно условие ⬜ → весь пайплайн работает на синтетике. `real_content_allowed: false`.

---

## 5. Полномочия

- **Есть:** запись в `learning.cognitive`, чтение captures из DP.SC.004, чтение w_reflections.
- **Нет:** чтение из `learning.cp_assessments` (объединение — задача Аттестатора), доступ к stage, certificate, billing.
- **Нет:** влияние на cp.rhy/cp.skl/cp.iwe/cp.int через любые механизмы (direct write, trigger, event).

---

## 6. Связи с другими ролями

| Роль | Отношение |
|------|-----------|
| DP.ROLE.041 Аттестатор | Потребитель результата (через cognitive_proxy_reader) |
| DP.ROLE.009 Портной | Потребитель результата (через cognitive_proxy_reader) |
| MIM.R.009 Диагност | Потребитель результата (через cognitive_proxy_reader) |
| DP.ROLE.001 IWE Creator | Owner Role в надсистеме |
| DP.ROLE.045 Dispatcher | Запускает пакетный анализ через Agent Inbox |

---

## 7. Режимы работы

| Режим | Триггер | Описание |
|-------|---------|----------|
| Потоковый | w_reflection_submitted | Анализ одной рефлексии после каждого Save |
| Пакетный | Agent Inbox cron (≤1 неделя) | Анализ накопленных captures за период |
| Синтетический (PII Gate открыт) | — | Только тестовые данные, real_content_allowed: false |
