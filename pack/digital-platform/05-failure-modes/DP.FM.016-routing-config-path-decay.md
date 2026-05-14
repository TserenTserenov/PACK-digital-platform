---
id: DP.FM.016
name: "Decay конфигурационных путей"
type: failure-mode
status: active
valid_from: 2026-05-09
source: extraction-report 2026-05-09 (aiogram middleware capture, inbox-check-2)
epistemic_stage: empirical
trust_level: medium
see_also: [DP.FM.005, DP.FM.007]
---

# DP.FM.016 — Decay конфигурационных путей (routing.md path staleness)

## Симптом

`config/routing.md § 5` содержит абсолютные пути к DS-репо. При переименовании или удалении репо файл не обновляется автоматически. Экстрактор пытается маршрутизировать capture → bounded context блокировка → DS-путь не найден → capture отложен.

## Механизм

```
routing.md (T0)  →  репо переименовано (T0+N)  →  routing.md не обновлён
                                                   ↓
                                     R2 получает стале путь при маршрутизации
```

## Последствия

- Capture не может быть маршрутизирован в DS-репо
- R2 возвращает `defer: репо не найдено`
- Knowledge не попадает в DS docs/

## Профилактика

1. При переименовании DS-репо → добавить в чеклист обновление `config/routing.md § 5`
2. Week Close: `grep` всех DS-путей из routing.md против `ls ~/IWE/` — верификация существования
3. routing.md не имеет механизма self-validation → компенсировать внешней проверкой

## Обнаружение

```bash
# Проверка DS-путей из routing.md на существование
while IFS= read -r path; do
  [ -d "$path" ] || echo "STALE PATH: $path"
done < <(grep "IWE/" ~/.iwe-runtime/roles/extractor/config/routing.md | grep -oE '/[^ ]+')
```

## Связанные паттерны

- DP.FM.005 (Model-Reality Drift): дрейф между конфигурацией и реальностью — общий мета-паттерн
- DP.FM.007 (View Drift): аналогично но для view-слоя данных
