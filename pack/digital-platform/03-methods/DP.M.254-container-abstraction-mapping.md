---
id: DP.M.254
name: "Container abstraction mapping — IT-аналогии через Persona+Память+Контекст без импорта docker-терминов"
type: method
domain: digital-platform
pack_refs: [DP.D.052]
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "peer-сессия 2026-05-29-14-mim-as-it-company, Тема 2 «Аналогия 'контейнер'»"
---

# DP.M.254 Container abstraction mapping

## Описание

Метод mapping cloud-native абстракций (docker container, Pod, volume) на knowledge/agent системы через **существующие** user-data слои (Persona / Память / Контекст по HD #27, DP.D.052) — без импорта docker-терминологии в имена доменных сущностей.

## IPO

**Вход:** целевая IT-абстракция (image / instance / env / volume / network / pod) + bounded context целевой системы
**Процесс:**
1. Выделить компоненты абстракции
2. Для каждого компонента найти существующую user-data сущность с эквивалентной семантикой
3. Использовать аналогию как **meta-язык** архитектурного анализа, оставляя собственные имена

**Выход:** mapping-таблица «IT-абстракция ↔ доменная сущность», без переименования доменных сущностей в IT-термины.

## Эталонный mapping (МИМ — Software factory)

| Docker | Семантика | Доменная сущность IWE |
|--------|-----------|----------------------|
| **Image** | Immutable snapshot | git commit универсального руководства + версия CAT |
| **Container instance** | Runtime state | Persona пилота при чтении |
| **Environment variables** | Config | cp-profile / bottleneck / active_rps |
| **Persistent volume** | Persistent state | managed repo mim-guides/pg-{uuid8} |
| **Network namespace** | Isolation | Local Gateway file-lock |
| **Pod** | Co-scheduled unit | Multi-agent session |

## Запрет

Переименовывать WP/роли в «Container Runtime», «Image Builder», «Pod Manager» — нарушение S-13 (имя = существительное-артефакт **домена**, не IT-должность). Аналогия живёт в архитектурном описании, не в названиях артефактов.

## Тест применимости

«Система претендует на сравнение с cloud-native стэком (есть immutable snapshots, runtime instances, isolation, persistent state)?»
- Да → применять DP.M.254 (mapping без импорта терминов)
- Нет → IT-аналогия не нужна, использовать доменные термины напрямую

## Связи

- DP.D.052 — Persona ≠ Память ≠ Контекст (основа mapping)
- S-13 — имя РП = существительное-артефакт (запрет на переименование в IT-термины)
