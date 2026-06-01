---
id: DP.M.193
name: Гибридный фикс — regex tolerance + локальная унификация
type: method
domain: DP
status: draft
valid_from: 2026-05-27
source_session: peer-сессия 20 (wp358-f9-status-regex-uniform, Темы 1+4)
pack_refs:
  - source: DP.METHOD (refactor patterns)
---

# DP.M.193: Гибридный фикс — regex tolerance + локальная унификация vs глобальный refactor

## Контекст

При cross-component inconsistency (N writer + M reader пишут/читают разные форматы) есть два пути:
- **Глобальный refactor** — унифицировать формат во всех компонентах. High regression risk, требует ArchGate.
- **Гибридный фикс** — двойная защита локально без масштабного refactor.

Метод описывает второй путь и условия его применения.

## Алгоритм

1. **Regex tolerance в потребителе.** Расширяем regex чтения, чтобы он принимал оба формата (старый + новый). Защита на стороне reader'а.
2. **Локальная унификация записи.** В одном компоненте (≤3 строки, single file) переводим writer на канонический формат. Не затрагиваем остальные writer'ы.
3. **TODO в коде.** В точке локального фикса оставляем `# TODO: глобальная унификация — backlog РП-NNN, ArchGate-trigger`.
4. **Бэклог-РП.** Заводим РП на глобальную унификацию с явным указанием: ArchGate триггер + scope (все writer'ы + миграция данных).

## Граница применимости

**Уместен, когда:**
- Глобальный refactor требует ArchGate (затрагивает контракт между сервисами/режимами).
- Локализованный change → низкий P95 регрессии.
- Backlog ведётся явно (TODO виден в коде).

**Не уместен, когда:**
- Format drift накапливается линейно с числом потребителей — гибрид замедлит миграцию.
- Tolerance расширяет surface для багов (новый формат семантически конфликтует со старым).

## Применимость

- Cross-microservice API inconsistency.
- Кросс-БД serialization mismatch.
- Мульти-парсер data format drift.
- Любые N writer + M reader с разными форматами.

## Источник

Peer-сессия 20 «wp358-f9-status-regex-uniform» (2026-05-27), Темы 1+4.
