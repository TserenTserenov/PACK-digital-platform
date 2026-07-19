---
id: DP.D.084
name: "Workspace-координация peer'ов ≠ Conversational-сессия peer'ов"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-05-22
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.084: Workspace-координация peer'ов ≠ Conversational-сессия peer'ов

| Аспект | Workspace-координация (DP.SC.034, DP.SC.035) | Conversational-сессия (DP.SC.154) |
|--------|----------------------------------------------|------------------------------------|
| **Объект работы** | Разные файлы workspace | Одна задача, один отчёт |
| **Режим** | Асинхронный, parallel | Синхронный, turn-taking |
| **Координация** | File lock + peer-status (focus, intent) | Журнал реплик + детектор `ESCALATE_TO_USER` |
| **Транспорт** | Local Gateway (Unix socket) + git | Файловый журнал `agent-conversation/<task-id>/` |
| **Результат** | Каждый peer коммитит своё | Один общий `report.md` от писателя |
| **Метафора** | Команда инженеров делит модули | Команда обсуждает решение и докладывает |
| **Эскалация к пилоту** | По `awaiting_decision` в peer-status | По маркеру в реплике |
| **Pacing** | Долговременный (часы/дни сессии) | Короткий (минуты-десятки минут, ходы по 30-60с) |

**Тест выбора:** «Делят ли агенты workspace (разные файлы) или обсуждают одну задачу (один результат)?» → разные → workspace; один → conversational.

**Почему важно:** В workspace-координации разногласия разрешаются через изоляцию focus (каждый peer работает над своим). В conversational-сессии разногласия — естественная часть процесса, цель — согласованный отчёт через многотуровый обмен аргументами. Смешение двух режимов в одном протоколе ведёт к ложным lock-конфликтам (peer'ы блокируют друг друга на одной задаче) или к unilateral-решениям (один peer молча перезаписывает другого).
