id: DP.IWE.012
name: Talk Mode — голосовой surface поверх IWE-сессии
type: domain-entity
status: active
created: 2026-06-01
trust:
  F: 2
  G: domain
  R: 0.6
epistemic_stage: evidence
related:
  specializes: [DP.IWE.004]
  uses: [DP.IWE.001, DP.IWE.005]
---

# DP.IWE.012 — Talk Mode

## 1. Определение

Talk Mode — голосовой overlay поверх существующей IWE-сессии: STT-вход (speech-to-text) на стороне клиента + TTS-выход (text-to-speech) на ответ агента, in-band с текущим текстовым каналом.

## 2. Различение

Talk Mode ≠ голосовой бот.

- **Голосовой бот** — отдельный канал (Telegram voice notes, отдельный handler).
- **Talk Mode** — overlay поверх той же сессии: пользователь говорит вместо набора, агент озвучивает вместо тишины. Текстовый transcript сохраняется как первичный артефакт.

## 3. Контракт

| Атрибут | Описание |
|---------|----------|
| **Trigger** | Активация (горячая клавиша / команда / голосовое слово) |
| **Вход** | Аудиопоток → STT → text → передача в текущую сессию |
| **Выход** | Text-ответ агента → TTS → аудиопоток клиенту |
| **SLA latency** | First-audio-byte ≤ 1с (TTS streaming) |
| **Failure mode** | STT error / TTS error → graceful fallback на текст в той же сессии (см. DP.M.255) |

## 4. Связь с DP.IWE.004

Talk Mode — модификатор существующих surface'ов (VS Code, Браузер, Бот), не седьмой surface. Колонка «Talk Mode» в таблице DP.IWE.004 будет добавлена при реализации (WP-384).

## 5. Источник

WP-384 «Голосовой интерфейс IWE (Talk Mode)» — РП создан 2026-06-01.
~~~

**Вердикт:** accept
**Обоснование:** entity-определение для нового surface-overlay. Без него последующие SC/methods (DP.SC по triggers, DP.M по STT/TTS-адаптерам) будут безымянными. Trust=evidence (РП создан, реализации нет).

---

## Сводка

| Метрика | Значение |
|---------|----------|
| Captures обработано | 4 |
| Всего кандидатов | 4 |
| Accept | 3 |
| Reject | 1 |
| Defer | 0 |
| Осталось в inbox | 0 |

### Назначенные ID

- DP.M.255 — Trigger-паттерн memory-провайдера
- DP.FM.119 — Bulk KE ID collision
- DP.IWE.012 — Talk Mode

### Следующие свободные ID (после accept)

- DP.M.256
- DP.FM.120
- DP.IWE.013

### Отклонения

- Cold-context review для bulk Pack commit (P-KE-008 already-in-pack: `memory/lessons_subagent_review_for_validators.md` §Bulk-apply 2026-06-01).
