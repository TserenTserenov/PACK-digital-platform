# CLAUDE.md — PACK-digital-platform

> **Общие инструкции:** см. `/Users/tserentserenov/Github/CLAUDE.md`
>
> Этот файл содержит только специфику данного репозитория.

---

## 1. Тип репозитория

**Pack** — source-of-truth для области «Цифровая платформа развития интеллекта».

**Является source-of-truth** — downstream репозитории берут знания отсюда.

---

## 2. Иерархия источников (Fallback Chain)

```
1. Этот Pack (предметное знание)
   └── pack/digital-platform/
2. SPF (форма и процесс)
   └── ~/Github/SPF/
3. FPF (первые принципы) — только если нет в SPF
   └── ~/Github/FPF/FPF-Spec.md
```

**Правило:** Если чего-то нет на текущем уровне — смотри выше.

**Когда обращаться к SPF:**
- При вопросах о структуре Pack'а
- При вопросах о процессе (SoTA, distinctions, methods)
- При проверке корректности оформления

**Когда обращаться к FPF:**
- Только если SPF не покрывает вопрос
- Для базовых различений (A.7: Method ≠ Tool, Object ≠ Description)

---

## 3. Структура Pack'а

```
pack/digital-platform/
├── 00-pack-manifest.md        # Метаданные
├── 01-domain-contract/        # Контракт домена
│   ├── 01A-bounded-context.md
│   └── 01B-distinctions.md
├── 02-domain-entities/        # Сущности
├── 03-methods/                # Методы
├── 04-work-products/          # Рабочие продукты
├── 05-failure-modes/          # Типовые ошибки
├── 06-sota/                   # SoTA-статусы
└── 07-map/                    # Карты связей
```

---

## 4. Hard Bans (из SPF)

- **Нет дидактики:** "step", "lesson", "module" — это downstream
- **Нет путаницы типов:** Method ≠ Tool, System ≠ Episteme

---

## 5. Роль Claude

| Делает | НЕ делает |
|--------|-----------|
| Помогает формализовать знания | Не изобретает предметное содержание |
| Следит за соответствием SPF | Не создаёт downstream-артефакты |
| Проверяет SoTA-статусы | Не добавляет дидактику |

---

## 6. Связи

| Репозиторий | Роль |
|-------------|------|
| SPF | Upstream — форма и процесс |
| FPF | Upstream (через SPF) — первые принципы |
| DS-ecosystem-development | Downstream — управление экосистемой |
