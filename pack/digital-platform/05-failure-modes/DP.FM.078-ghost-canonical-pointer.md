---
id: DP.FM.078
name: Ghost canonical pointer
type: failure-mode
trust: medium
epistemic_stage: observed
status: active
created: 2026-05-23
sources:
  - session-close 2026-05-23 #9 (day-open-verify, Kimi ход 1)
  - инцидент: Day Open scaffold ссылался на stale memory/templates-dayplan.md (270 строк) вместо актуального exocortex/templates-dayplan.md (355 строк)
---

# DP.FM.078: Ghost canonical pointer

## Симптом

В системе с mirror'ами/копиями документа canonical-указатель (поле в spec/manifest/skill) ведёт на физически существующий, но **устаревший** mirror, тогда как реальный source-of-truth — другой файл, который продолжает обновляться.

Автоматизация, читающая canonical-указатель, получает stale content. Все обычные проверки целостности (file exists, valid format, parseable) проходят: оба файла валидны, оба существуют.

## Корневая причина

Каноническая ссылка зафиксирована в moment T1, реальное обновление переехало в другой файл в moment T2 > T1, ссылка не обновилась. Имя `ghost canonical` — указатель «живёт», на самом деле указывает на призрак.

## Пример (Day Open scaffold, 2026-05-22)

- `memory/templates-dayplan.md` — 270 строк, stale (не обновлялся ~2 недели)
- `exocortex/templates-dayplan.md` — 355 строк, актуальный (обновлялся неделю назад)
- SKILL.md указывал на первый как canonical → DayPlan генерировался по устаревшему шаблону
- Pre-commit hook не ловит (оба файла валидны)
- File-integrity проверки проходят (mtime, hash — оба valid)

## Детектор

Периодически hash-diff между **указанным** canonical и **фактической** точкой обновления:

```bash
canonical_hash=$(git log -1 --format=%H -- "$CANONICAL_PATH")
mirror_hash=$(git log -1 --format=%H -- "$MIRROR_PATH")
canonical_mtime=$(stat -f %m "$CANONICAL_PATH")
mirror_mtime=$(stat -f %m "$MIRROR_PATH")

if [ "$mirror_mtime" -gt "$((canonical_mtime + 7*86400))" ]; then
  echo "ALERT: mirror $MIRROR_PATH обновляется чаще canonical $CANONICAL_PATH (>7d skew)"
fi
```

## Тест

«Если изменить файл-источник, обновится ли тот, на который ссылается canonical-pointer?»
- Нет → ghost canonical pattern.
- Да → canonical верен (он и есть источник).

## Область применения

- Spec / SKILL.md с pointer-полями (canonical_template, source_of_truth, schema_path)
- Документация с symlink-указателями на «актуальную версию»
- CDN/repo mirror с manifest, указывающим неправильный origin
- Config с symlinks, переехавшими на новый путь
- Cache invalidation, ссылающаяся на устаревший cache-key

## Лечение

1. Hash-diff alert при skew >X дней (см. детектор)
2. CI-job, парсящий все canonical-указатели и проверяющий, что они = git log most-recently-modified для своего класса
3. При обнаружении: обновить указатель или удалить stale-mirror (single point principle, DP.M.160)

## Связи

- DP.M.160 — Single point of degradation tracking (структурная профилактика)
- OwnerIntegrity (CLAUDE.md): один факт — одно место.
