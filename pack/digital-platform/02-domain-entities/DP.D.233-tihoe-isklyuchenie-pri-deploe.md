---
id: DP.D.233
name: "Тихое исключение при деплое (silent-exclude) ≠ Уведомление об устаревании (notify-deprecate)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-07-11
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.233: Тихое исключение при деплое (silent-exclude) ≠ Уведомление об устаревании (notify-deprecate)

В системах распространения шаблона два механизма с похожим внешним эффектом «файл не доезжает / считается устаревшим», но разной семантикой:
- **Silent-exclude:** файл игнорируется при деплое, потребитель не уведомлён (тихо).
- **Notify-deprecate:** потребителю явно сигнализируется удалить файл вручную — сигнал повторяется при каждой проверке (`--check`), пока файл не удалён.

**Тест:** «Скрыть файл при деплое или предупредить потребителя, что его нужно удалить?» Скрыть молча → silent-exclude. Явно предупредить с повтором до устранения → notify-deprecate.

**Связано с:** DP.FM.075 (deprecated_files как TODO-tracker — конкретная реализация половины пары «notify-deprecate»).

**Источник:** capture session-close 2026-06-30 (`update-manifest.json`: поля `deprecated_files` vs `excluded_paths`); решение о Pack-маршруте (абстрактно, без привязки к именам полей) — пилот, 2026-07-11.
