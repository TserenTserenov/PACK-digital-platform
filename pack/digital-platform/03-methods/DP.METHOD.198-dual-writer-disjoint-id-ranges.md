---
id: DP.METHOD.198
name: "Dual-writer с дизъюнктными диапазонами ID — управляемый паттерн"
type: method
pack: PACK-digital-platform
domain: digital-platform / data-architecture
kind: Method
status: active
created: 2026-07-15
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
sources:
  - "session-close 2026-07-10; WP-183 ArchGate Directus (report.md §2 Тема 3, §6, commit c2747249c)"
related:
  see_also: [DP.FM.299]
schema_version: 1
---

# DP.METHOD.198 — Dual-writer с дизъюнктными диапазонами ID

## Определение

Два независимых писателя (машинный конвейер + ручной ввод) работают в одну таблицу через **непересекающиеся диапазоны ключей**. Структурного конфликта нет — это управляемый постоянный паттерн, принципиально отличный от двусторонней синхронизации.

## IPO

- **Вход:** одна таблица, два независимых источника записи (машинный + ручной)
- **Процесс:** разбивка диапазонов ID + отдельная sequence для каждого источника + trigger-энфорсер
- **Выход:** таблица с изолированными диапазонами без sync-логики и гонок

## Реализация

```sql
-- Машинный конвейер: sequence от 1 (стандартная)
-- Ручной ввод (Directus): sequence от 900_000_000
CREATE SEQUENCE directus_entries_seq START 900000000;

-- Trigger-энфорсер непересечения
CREATE OR REPLACE FUNCTION enforce_id_range()
RETURNS TRIGGER AS $$
BEGIN
  IF NEW.id < 900000000 THEN
    RAISE EXCEPTION 'Manual entries must use ID >= 900000000';
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER check_directus_id_range
BEFORE INSERT ON entries
FOR EACH ROW
WHEN (NEW.source = 'directus')
EXECUTE FUNCTION enforce_id_range();
```

## Отличие от двусторонней синхронизации

| Паттерн | Риск | Класс |
|---------|------|-------|
| **Dual-writer с дизъюнктными ID** (этот) | Управляемый, структурный | Постоянный паттерн |
| Двусторонняя синхронизация двух копий | Конфликты при concurrent writes | Источник гонок |

Ключевое: dual-writer с дизъюнктными ID = **не синхронизация**, а изоляция в одной таблице.

## Прецедент

WP-183: `aist_me_bot_writer` (машинный) + Directus (ручной) — стабильно с апреля 2026 в production. Принято как постоянное архитектурное решение, не временная мера.

## Когда применять

- Машинный конвейер + ручной ввод оператора в одну таблицу
- Не требуется sync между источниками (каждый пишет в свой диапазон)
- Нужна изоляция без отдельного сервиса/схемы
