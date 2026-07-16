---
id: DP.METHOD.179
name: SoTA-check at Pack creation step 1.5 to prevent domain fragmentation
type: method
status: active
scope: Pack creation process, domain design
severity: medium
introduced_date: 2026-07-10
valid_from: 2026-07-16
---

# DP.METHOD.179: Pack creation step 1.5 — SoTA domain check

## Назначение

Вставить явный шаг между "именование Pack'а" и "инициализация структуры" для проверки: не является ли целевой домен уже поддоменом существующего Pack'а. Цель: предотвратить фрагментацию знания по отдельным Pack'ам, когда тема реально является subdomain уже зарегистрированного Pack'а.

## Шаг 1.5 алгоритм

### Вход

Название и домен предполагаемого нового Pack'а (e.g. "PACK-rhetoric: риторика речи").

### Процесс

1. **Поиск в sota_sources смежных Pack'ов** по ключевым словам домена
   - grep по названиям существующих Pack'ов
   - grep по их описаниям в 00-pack-manifest.md
   - поиск в PACK-digital-platform/08-sota/ по ключевым словам

2. **Анализ:** находится ли домен как подтема?
   - Пример: "rhetoric" уже есть в PACK-personal (§9 методы саморазвития) или PACK-digital-platform (§8 коммуникация)
   - Пример: "failure-modes autistic agents" может быть subdomain PACK-autonomous-agents

3. **Принятие решения**
   - **Если find:** добавить sota_sources в существующий Pack, не создавать новый
   - **Если no find:** forward к шагу 2 (инициализация структуры новго Pack'а)

### Выход

Либо: "Add to existing <Pack-ID>.sota_sources" / Либо: "Proceed with new Pack creation"

## Примеры

**Гипотетический случай (что было бы обнаружено на Шаге 1.5):**
- Требуемый Pack: "PACK-rhetoric: Риторика визуальной коммуникации"
- Поиск: в PACK-personal уже есть "speech, rhetoric, persuasion" в 07-formalizations
- Решение: не создавать PACK-rhetoric, добавить sota_sources/новые формализации в существующий PACK-personal

**Контекст реализации (WP-474):**
Процесс создания Pack интегрирован в /pack-new skill. На Шаге 1.5 система предлагает выбор.

## Синхронизация

- **Kind-термины:** обновлены в create-flow (указание типа Pack'а)
- **/verify pack:** argument-hint включает список существующих Pack'ов
- **SPF/pack-template/:** версионирован процесс с явным Шагом 1.5

## Связанные сущности

- WP-474 Ф1-Ф6 (реализация в FMT-exocortex-template)

## Переносимость

Применимо к любой системе с множественными доменами (Enterprise Architecture, library ecosystem):
- Перед регистрацией нового "модуля"/"компонента" → проверить не является ли он подмодулем существующего
- Предотвращает proliferation похожих, но разрозненных единиц знания
