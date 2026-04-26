---
id: DP.SOTA.015
name: "AI/LLM System Observability (3+1 Framework)"
type: sota
status: draft
summary: "SOTA-модель observability для AI/LLM: 3-сигнальная телеметрия (Traces/Metrics/Logs) + AI-специфичный слой Evaluations. «4-слойная AI observability» как именованный стандарт не существует."
created: 2026-04-12
edition: "2026-04"
trust:
  F: 3
  G: external
  R: 0.7
related:
  integrates_with: [DP.SOTA.008, DP.SOTA.013]
  referenced_by: [DP.FM.005]
  operationalized_by: [DP.SC.025]
tags: [observability, otel, llm-monitoring, ai-ops, evaluation]
---

# DP.SOTA.015 — AI/LLM System Observability (3+1 Framework)

## Метаданные

draft | 2026-04-12 | источник: R23 Scout → WP-170 разбор #7

## Статус SOTA

Emerging, industry consensus forming (2025–2026). OpenTelemetry GenAI SIG ведёт финализацию стандарта — консенсус о модели (3+1) уже сложился, но спецификация ещё в разработке.

## Суть

SOTA-модель observability для AI/LLM систем (2025–2026) — это **3-сигнальная телеметрия + слой Evaluation**, а не «4-слойная модель». Три телеметрических сигнала унаследованы из классической observability, четвёртый компонент — AI-специфичное расширение.

## Модель

| Компонент | Описание | Инструмент |
|-----------|----------|------------|
| **Traces** | End-to-end пути исполнения: prompt → LLM → tool call → retrieval → response. Структура spans. | OTel, Langfuse, Arize |
| **Metrics** | Агрегированные сигналы: latency, token usage, cost, error rates, throughput | OTel, Helicone |
| **Logs** | Структурированные записи событий с correlation IDs | OTel, любой backend |
| **Evaluations** (AI-specific) | LLM-as-a-Judge, human annotation, автоматические evals; drift detection; factuality/relevance/safety scoring | Arize Phoenix, WhyLabs, Langfuse |

## Стандарт

**OpenTelemetry GenAI SIG** — emerging universal standard:
- AI Agent Application Semantic Convention: финализирована (основа — Google AI agent whitepaper)
- AI Agent Framework Semantic Convention: в разработке (CrewAI, AutoGen, LangGraph)

OTel = universal interoperability layer: любой агентный фреймворк → стандартизированная телеметрия → любая observability платформа.

## Сравнение платформ

| Платформа | Модель | Стандарт |
|-----------|--------|----------|
| Langfuse | Trace-first, OTel-native | OpenTelemetry |
| Arize Phoenix | OTel + OpenInference | OpenTelemetry |
| WhyLabs/LangKit | Statistical profiling + drift | whylogs, без OTel |
| Helicone | Proxy-first, lightweight logs | Без OTel |

Neptune.ai приобретён OpenAI (2026) — сигнал консолидации рынка.

## Важное различение

«4-слойная AI observability» (логи/метрики/трейсы/качество) как **именованный отраслевой стандарт** — не существует. Нет papers ICLR/NeurIPS/MLOps Community с этим названием. Встречается только в авторских practitioner-постах. Ссылаться как на SOTA нельзя.

## Failure Mode

Смешение: классическая infra observability (3 pillars, Сридхаран 2018) ≠ AI system observability (3+1 framework). Drift/degradation monitoring = часть Evaluation layer, не отдельный pillar.

## Источники

- OpenTelemetry Blog: https://opentelemetry.io/blog/2025/ai-agent-observability/
- Langfuse vs Phoenix: https://langfuse.com/faq/all/best-phoenix-arize-alternatives
- Portkey Complete Guide: https://portkey.ai/blog/the-complete-guide-to-llm-observability/
- ResearchGate Model Monitoring Review: https://www.researchgate.net/publication/395703466_

## Обновление Q1-Q2 2026 (Scout 23 апр, rt-005)

### Платформенная таксономия 2026 — 3 лагеря

| Лагерь | Платформы | Фокус |
|--------|-----------|-------|
| **Traditional APM** | Datadog, New Relic | LLM-вкладки поверх существующей observability: токены + latency, без глубокой трассировки |
| **AI-native tracing** | Langfuse, LangSmith, Arize Phoenix | Span-level tracing, prompt management, evaluations как first-class граждане |
| **AI gateways** | Helicone, Portkey | Routing + caching + cost control между приложением и провайдером (Traces побочно) |

Ни один лагерь в одиночку не покрывает 3+1 framework полностью. Gartner отмечает, что предприятия в 2026 комбинируют 2-3 платформы (APM + AI-native + gateway).

### Gartner Quality Shift 2026

> "Traditional observability focuses on speed and cost, but the priority is shifting toward quality: factual accuracy, logical correctness, sycophancy detection."

Quality Evaluation перестаёт быть отдельным post-hoc этапом и интегрируется в observability pipeline: инструменты оценивают outputs в production, алертируют на quality degradation, детектируют drift и возвращают insights в dev cycle. Это операционализирует четвёртый компонент (Evaluations) как **непрерывный**, а не периодический.

### Событие рынка: Langfuse + ClickHouse (Q1 2026)

Langfuse приобретён ClickHouse. Open-source модель сохраняется, появляется ClickHouse-backed storage для production-scale. Сигнал консолидации AI-native сегмента вокруг OLAP-backends.

### Статус 3+1 framework после Q1 2026

Framework подтверждён как отраслевой консенсус: Traces, Metrics, Logs (три унаследованных сигнала) + Evaluations (AI-специфичный слой). Quality Shift укрепляет именно четвёртый компонент — теперь он приоритетный, а не дополнительный.

## Прикладной паттерн: Notification ≠ Telemetry-dump (WP-271, 26 апр 2026)

> Применимо к **Grade-0/1 системам** (скрипты, cron-задачи без LLM): Setup Agent, Synchronizer, Scout, Стратег. У них нет полноценного 3+1 framework — только Logs + Notifications. Но именно поэтому notification-канал критичен: он единственный сигнал к пользователю.

**Failure mode:** Notification = дамп счётчиков из лога. Два разных запуска визуально идентичны → пользователь читает их как баг-дубль.

**Принцип:** Notification = сигнал к действию. Сообщение должно нести:
- **Что случилось** (статус: success/failed-terminal/failed-transient/recovered)
- **Минимум контекста** для принятия решения (имя файла, commit hash, причина FAIL)
- **Action-needed** при failure (что именно делать, не просто факт)

**Паттерн «Retry Aggregation»** для catch-up retry-loop'ов (cron-задачи с N wake-up'ов в день):
- 1-я попытка FAIL → 1 TG алерт «attempt 1 failed»
- 2-я, 3-я попытка FAIL → silent (только лог)
- N-я попытка ≥ threshold → 1 TG алерт «giving up after Nx» + mark_done (терминальный)
- SUCCESS после fail-streak → 1 TG алерт «recovered after Nx»

**Антипаттерн:** «N wake-up'ов = N TG-сообщений». Шумовая буря десенсибилизирует пользователя; он перестаёт читать TG, пропускает реальный инцидент.

**Реализация (reference):**
- [scheduler.sh § Retry aggregation](../../../../DS-IT-systems/DS-ai-systems/synchronizer/scripts/scheduler.sh) — `handle_transient_failure` / `handle_terminal_failure` / `handle_success_after_retry`
- [setup-agent.sh § build_message](../../../../DS-IT-systems/DS-ai-systems/synchronizer/scripts/templates/setup-agent.sh) — обработка RETRY_EVENT (first-fail/giving-up/recovered/terminal)
- [template-sync.sh § Sync Report](../../../../DS-IT-systems/DS-ai-systems/setup/scripts/template-sync.sh) — RUN_ID + COMMIT + UPDATED_FILES якоря для парсера

**Связанное различение:** «Терминальный fail ≠ Transient fail в catch-up retry-loop'ах» (`distinctions.md`, 26 апр).

## Связанные сущности

- DP.SOTA.008 (Real-Time Knowledge Capture)
- DP.FM.005 (Model-Reality Drift)
- DP.SC.025 (Capture-шина)
- WP-45 (Ф4 — dep:WP-109, observability layer)
