---
id: DP.SC.145
title: LLM Router — выбор модели и автоматический фоллбэк
domain: DP.IWE
created: 2026-04-30
status: active
related_wp: [200]
note: Реализация через WP-200 (LLM Proxy Service) в рамках зонтика WP-150
---

# DP.SC.145: LLM Router

## Обещание

Система LLM Router маршрутизирует запросы к различным LLM-моделям (Claude, GPT, Gemini) и гарантирует результат, используя автоматический фоллбэк при недоступности.

### Триггер

Пользователь или агент запускает задачу через:
- Claude Code CLI (`--model gpt-4`)
- Claude Code Settings (`model: "auto"`)
- Бот-запрос (автоматический выбор по типу задачи)
- Серверный агент (явный выбор модели или auto-selection)

### Входы

- **prompt:** исходный промпт пользователя (текст)
- **model_preference:** явный выбор модели или `"auto"` для автоматического выбора
  - Явные: `"claude-opus"`, `"claude-sonnet"`, `"claude-haiku"`, `"gpt-4"`, `"gpt-4-turbo"`, `"gemini-2.0"`
  - Auto: Router выбирает на основе heuristic (размер промпта, тип задачи)
- **fallback_enabled:** boolean (по умолчанию true)
- **timeout_sec:** таймаут на один вызов (по умолчанию 30)

### Выходы

- **result:** ответ от выбранной модели (текст)
- **model_used:** какая модель была использована (строка)
- **tokens_prompt:** количество токенов входа
- **tokens_completion:** количество токенов выхода
- **cost:** приблизительная стоимость вызова (USD)
- **fallback_applied:** boolean (была ли применена замена модели)

### Время отклика

- Выбор модели: ≤500ms (при auto-selection heuristic)
- Вызов LLM: зависит от модели (Haiku ~2s, Sonnet ~5s, Opus ~10s, GPT ~4-8s)
- Фоллбэк: дополнительно +4-5s на переключение (но не блокирует пользователя, может быть async)
- **SLA:** результат должен прийти за ≤60s (включая timeout и fallback)

### Инвариант

**Гарантия:** пользователь ВСЕГДА получит результат или явную ошибку, никогда молчаливого отказа.

**Детали:**
1. Если выбранная модель доступна → результат от неё (best-path)
2. Если выбранная модель недоступна (timeout, rate-limit, error) и fallback=true:
   - Попытка 2: альтернативная модель (Claude → GPT, GPT → Claude)
   - Если успех → результат (с пометкой `fallback_applied=true`)
   - Если опять fail → Попытка 3: third-choice модель
3. Если все 3 попытки failed → graceful error (не 503 Service Unavailable, а structured JSON с reason и advice)

**Согласованность:** один и тот же промпт при auto-selection выбирает ту же модель (детерминированно).

### Режим отказа (Failure Mode)

| Сценарий | Поведение | User-facing |
|----------|-----------|------------|
| Выбранная модель timeout | Retry 1x, если fail → fallback | "Модель неотзывчива, переключаюсь на альтернативу" |
| Rate-limit (429) | Fallback immediately | "Исчерпаны лимиты, используем другую модель" |
| API error (500+) | Fallback + logError | "Сервис недоступен, пробуем резервный" |
| Все модели недоступны | Error 503 + advice | "Все модели недоступны. Повторите попытку через 30с или используйте локальную модель" |
| Invalid model_preference | Error 400 + list of valid | "Неизвестная модель. Допустимые: claude-opus, gpt-4, ..." |
| Insufficient funds / quota | Error 402 + advice | "Исчерпан лимит токенов. Обновите подписку или подождите до завтра" |

---

## Сценарии использования

### Сценарий 1: Стратегирование с резервом (Opus недоступен)

**Участники:** Пользователь в Claude Code, система Router, платформа LLM Proxy

**Триггер:** Пользователь запускает `/strategy-session` в VS Code (по умолчанию выбор Opus для open-loop reasoning)

**Поток:**
1. Router проверяет `model_preference="claude-opus"` + `fallback_enabled=true`
2. Пробует вызвать Claude Opus через API
3. **Timeout (60s) → fallback triggered**
4. Router переключается на `claude-sonnet` (best fallback для reasoning)
5. Пользователь получает результат через 70 секунд с пометкой "fallback_applied: true"
6. Router логирует в `llm_usage`: user_id, opus→sonnet, cost (дешевле), duration

**Результат:** Strategy Session завершена (пусть и на Sonnet), пользователь видит, что произошло

---

### Сценарий 2: Быстрый review в боте (выбор по intent)

**Участники:** Пользователь в Telegram, бот, Router

**Триггер:** Пользователь пишет в бот "быстро посмотри мой код" (intent="quick_review")

**Поток:**
1. Бот определяет intent → `model_preference="claude-haiku"` (быстро и дёшево)
2. Router вызывает Haiku API
3. **Успех за 3 сек → результат**
4. Пользователь видит ответ
5. Router логирует: user_id, haiku, cost ~$0.001

**Альтернатива (если Haiku fail):**
- Fallback на `claude-sonnet` (более надёжный)
- Если Sonnet тоже fail → fallback на `gpt-4-turbo` (для second opinion)
- Результат может быть от другой модели, но пользователь видит какая использовалась

---

### Сценарий 3: Локальный экспериментальный режим (явный выбор GPT)

**Участники:** Dev в VS Code, локальный Router, OpenAI API

**Триггер:** Dev запускает `claude --model gpt-4 "архитектуру спроектируй"`

**Поток:**
1. Claude Code читает settings → `model: "gpt-4"`
2. Router получает явный выбор `model_preference="gpt-4"` + `fallback_enabled=false` (явный выбор = нет фоллбэка)
3. Вызывает GPT-4 API
4. Успех → результат
5. Если fail (и fallback_enabled=false) → error "GPT-4 недоступен, фоллбэк отключён"

**Рациональность:** Dev экспериментирует, хочет именно GPT, не случайный фоллбэк

---

## Критерии успеха (ready for production)

- [ ] Router модуль работает с ≥2 провайдерами (Claude + OpenAI)
- [ ] Fallback logic покрывает ≥3 типа ошибок (timeout, rate-limit, api-error)
- [ ] Auto-selection heuristic детерминирован (тот же промпт → та же модель)
- [ ] Логирование в `llm_usage` таблицу: user_id, model, tokens, cost, fallback_flag
- [ ] Error messages user-friendly (не technical stack traces)
- [ ] SLA соблюдается: ≤60s на результат или graceful error
- [ ] Smoke-test: 10+ вызовов, все успешны или возвращают graceful error

---

## Интеграция с платформой

**WP-200 LLM Proxy:** Router — клиент LLM Proxy (для платформенного учёта токенов и фоллбэка на платформе)

**WP-201 Серверные агенты:** Агенты используют Router для выбора модели внутри своего reasoning loop (например, Verifier может попросить second opinion от GPT)

**WP-150 Мультиагентность:** Разные агенты → разные модели (Стратег → Opus, Экстрактор → Sonnet, Верификатор → GPT для diversity)

---

*Source: WP-290 Ф0 IntegrationGate | Status: draft (ready for ArchGate) | Last updated: 2026-04-30*
