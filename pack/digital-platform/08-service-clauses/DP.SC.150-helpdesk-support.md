---
id: DP.SC.150
name: Поддержка пользователей IWE через @aist_me_bot + Chatwoot
type: sc
status: draft
layer: L4-Personal
summary: "Пилот через команду /support в @aist_me_bot открывает тикет в Chatwoot CE; служба поддержки получает структурированный контекст (telegram_id, ory_uuid, последние события), отвечает в Chatwoot — ответ доставляется в TG-чат пилота с префиксом 🛟; SLA ≤24ч на первый ответ"
consumer: Пилот IWE; DP.ROLE.055 Агент поддержки
created: 2026-05-20
updated: 2026-05-20
wp: WP-341
related:
  uses: [DP.SC.036]
  produces: [Chatwoot conversation в `chatwoot` Neon DB]
  reuses_role: [DP.ROLE.001]
---

# [DP.SC.150] Поддержка пользователей IWE через @aist_me_bot + Chatwoot

## Обещание

**Кому:** Пилот IWE (входная точка) и Агент поддержки DP.ROLE.055 (потребитель тикетов).

**Зачем:** Дать пилоту единый канал для обращений (баг / вопрос / пожелание / баллы / руководство) внутри уже знакомого @aist_me_bot, а службе поддержки — структурированную очередь тикетов с контекстом без ручного сбора данных из 3 БД.

**Что получит пилот:**
- Команду `/support` в @aist_me_bot с inline-формой (тема → описание → приоритет).
- FAQ-перехват: если найден шаблонный ответ — присылается до создания тикета.
- Уведомления о статусе тикета с префиксом 🛟 в том же чате.
- Возможность параллельно вести `/lesson`, `/day-open` (тикет приостанавливается, ответы саппорта приходят с пометкой).
- CSAT-опрос (1-5) при закрытии тикета.

**Что получит агент поддержки (DP.ROLE.055):**
- Тикет в Chatwoot веб-админке с автоназначением по теме.
- Карточку с `telegram_id`, `ory_uuid`, последние 10 событий из `activity_log`, статус подписки, история прошлых тикетов.
- Saved Replies для повторных тем.
- Кнопку «Create Linear Issue» при подтверждении бага.

**Критерий приёмки MVP:**
- Команда `/support` доступна в @aist_me_bot пилотного контура (Railway `aist_pilot_bot`).
- Первый тикет создаётся в Chatwoot и виден агенту ≤30 сек.
- Агент отвечает в Chatwoot, ответ доставляется в TG пилоту ≤10 сек.
- CSAT-опрос отрабатывает при `resolve`.

## Инварианты (Правила)

| Правило | Смысл |
|---------|-------|
| **Идентификация** | Один TG-аккаунт = один Chatwoot contact. Mapping `telegram_id ↔ chatwoot_contact_id` хранится в `users` боте (новая колонка). |
| **PII-gate B7.3** | Сообщения тикета содержат PII (имя, переписка). Хранение только в Neon `chatwoot` DB (EU-Central 1). Лог-уровни бота: `INFO` не пишет содержимое сообщения, только `conversation_id` + `length`. |
| **State isolation** | При параллельном `/lesson` обычный flow бота не ломается. Тикетные сообщения помечены префиксом 🛟. Открытый тикет хранится в `users.active_chatwoot_conv_id`. |
| **FAQ-first** | Перед созданием тикета — поиск в `chatwoot_articles` (FAQ-таблица). Если найден шаблон с similarity ≥ 0.7 — предложить пилоту. |
| **SLA** | Тикет без первого ответа >24ч → автоэскалация (TG Ильшату). Resolution >48ч — Дмитрию. |
| **Inbound resilience** | Если Chatwoot недоступен — бот буферизует сообщение в `chatwoot_outbox` (Neon `chatwoot.outbox`), retry экспоненциально (1m, 5m, 15m). |
| **Webhook HMAC** | Chatwoot → бот webhook на новом endpoint `/chatwoot-webhook` валидируется HMAC-SHA256 с секретом `CHATWOOT_HMAC_SECRET`. |

## Триггеры

| Триггер | Источник | Режим |
|---------|---------|-------|
| `/support` команда | Пилот в @aist_me_bot | Sync, inline-форма |
| Текстовое сообщение в режиме открытого тикета | Пилот | Sync, proxy в Chatwoot API |
| Webhook `conversation.created/updated/message_created` | Chatwoot → бот | Async, HMAC-валидация |
| Webhook `conversation.resolved` | Chatwoot → бот | Sync, шлёт CSAT в TG |
| n8n auto-ticket (health-monitor алерт) | n8n workflow | Server-side через Chatwoot API |

## Сценарии использования

1. **Типовой баг (баллы не пришли).** Пилот: `/support` → тема=баллы → описание → приоритет=средний → бот: «✅ #142 открыт, Ильшат ответит ≤24ч». Час спустя: «🛟 Ильшат: проверил, перезапустил проектор. Подтвердите?» → пилот: «да» → бот: «✅ #142 закрыт. Оцените 1-5».

2. **FAQ-перехват.** `/support` → «вопрос» → «как создать репо?» → бот: «📚 Из FAQ: [текст]. Подошло? (да / нет)». Если «да» — тикет не создаётся.

3. **Параллельная активность.** Тикет #142 открыт, пилот пишет `/lesson` → бот: «У вас #142. (1) Приостановить (2) Закрыть сначала (3) Отмена». После занятия сообщения тикета продолжают приходить с 🛟.

4. **Срочное.** `/support срочно` → пропуск формы → описание → автотег `[priority/high]` → TG-алерт дежурному параллельно с Chatwoot-уведомлением.

5. **Несколько тикетов.** При открытом #142 пилот шлёт `/support` → бот: «У вас #142. (1) Добавить к нему (2) Открыть отдельный».

6. **Эскалация в Linear (агент).** Дима подтверждает баг → кнопка «Create Linear Issue» в Chatwoot → n8n workflow создаёт issue → тикет получает тег `[L-456]` → бот пилоту: «🛟 Зафиксировано как L-456, ETA 2 дня».

7. **Health-alert → автотикет.** n8n воркер фиксирует «бот не отвечает >5 мин у пользователя X» → создаёт Chatwoot тикет (без TG-уведомления пилоту X) → автоназначение дежурному.

## Out-of-scope MVP

- Голосовые сообщения от пилота (Chatwoot API Channel требует upload→URL).
- Multi-language (фиксируется русский).
- Round-robin маршрутизация по нагрузке (MVP — по теме).
- Saved Replies статистика и ML-suggest.

## Реализация

- **Bot:** новый handler `/support` в `aist_me_bot`, новые env vars `CHATWOOT_URL`, `CHATWOOT_API_TOKEN`, `CHATWOOT_HMAC_SECRET`.
- **БД:** колонки `users.chatwoot_contact_id` (int), `users.active_chatwoot_conv_id` (int nullable); таблица `chatwoot_outbox` (id, conv_id, payload, attempts, next_retry_at).
- **Chatwoot:** Railway peaceful-vision, сервисы `chatwoot-redis`, `chatwoot-web`, `chatwoot-sidekiq`. DB — Neon `chatwoot` (создана WP-341 Этап 0).
- **n8n:** workflows `health-alert-to-chatwoot` (Ф2), `chatwoot-to-linear` (Ф2).

## История

| Дата | Событие |
|------|---------|
| 2026-05-20 | Service Clause создан (WP-341 Этап 0 закрыт, Этап 1 стартует). Выбран вариант B (API Channel + @aist_me_bot) после развёрнутой развилки. |
