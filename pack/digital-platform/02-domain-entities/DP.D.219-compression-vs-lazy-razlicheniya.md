---
id: DP.D.219
name: "Compression ≠ Lazy — различения без advance-signal требуют split hot/warm, а не lazy-load"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-07-10
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.219: Compression ≠ Lazy — различения без advance-signal требуют split hot/warm, а не lazy-load

Два разных приёма снижения eager-стоимости каркасного компонента. Выбор определяется наличием детерминированного advance-signal.

| Lazy-load (отложенная загрузка) | Compression (сжатие через split) |
|----------------------------------|----------------------------------|
| Грузит полный контент по триггеру | Держит горячим самое частое + поиск для редкого |
| Требует детерминированный advance-signal (lexical prefix) | Работает без сигнала |
| Применимо: роли, скиллы, формат-правила | Применимо: distinctions (агент не знает заранее, какое понадобится) |
| Полный контент в сессии, где триггер сработал | Hot-часть всегда; warm-часть по `/distinction <термин>` или semantic-search |

**Почему различение важно.** distinctions.md нельзя сделать lazy через prefix: нет advance-signal — агент не знает заранее, какое из ~147 различений понадобится. Решение — не «загрузить когда понадобится», а split: hot-часть (≤150 строк, 15-20 самых горячих) + warm-часть (остальные, доступны через `/distinction <термин>` или semantic-search).

**Тест:** «Есть ли детерминированный advance-signal для этого компонента?»
- Да → lazy-load (DP.METHOD.146).
- Нет → split hot/warm (compression).

**Связано с:** DP.METHOD.146 (activate-on-prefix lazy); DP.FM.235 (eager framework context bloat — общая проблема, которую оба приёма лечат); DP.D.220 (режим отказа lazy-компонента ≠ блокировка).

**Источник:** session-transcript 2026-06-26 (WP-445 Ф3 ArchGate, peer-session 2026-06-26-02); git diff DS-my-strategy 321fa9fa9.
