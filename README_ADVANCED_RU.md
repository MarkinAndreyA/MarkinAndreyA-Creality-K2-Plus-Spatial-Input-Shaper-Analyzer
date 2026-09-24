# K2 Spatial Analyzer — Advanced руководство

**Advanced редакция · V1.0.0 · 23.09.2026**

Расширенная инструкция: параметры, движки, Measurement Gate, артефакты, диагностика и write-safety.

[Обычная RU](README_RU.md) · [English](README_ADVANCED_EN.md)

## Как пользоваться этим руководством

Advanced-редакция рассчитана на пользователей, которые хотят понимать не только последовательность действий в GUI, но и то, какие данные, проверки и алгоритмические контуры стоят за каждой операцией. Она также подходит для диагностики, подготовки issue в GitHub и воспроизводимого тестирования.

> **Важно:** «Обычная» и «Advanced» — это две редакции документации, а не два режима приложения. Интерфейс K2 Spatial Analyzer один и тот же.

## Что умеет K2 Spatial Analyzer

- принимать Spatial Measurement как архив TAR/TAR.GZ/TGZ/ZIP или как папку с RAW-данными;
- рассчитывать рекомендации Input Shaper и общий рекомендуемый `max_accel` по доступным AX/AY измерениям;
- использовать `K2_NATIVE_EXACT`, `cleanroom_upstream_2026` и `UPSTREAM_EXACT`;
- опционально ускорять RAW Welch FFT/PSD через CuPy/CUDA; при недоступности CUDA работать на CPU;
- строить HTML/PDF/PNG-отчёты и сравнительный анализ COLD / LOW_TEMP / HIGH_TEMP;
- проводить Measurement/Belt Gate и пространственный сбор RAW на K2 Plus;
- записывать рассчитанный K2_NATIVE Input Shaper через отдельную защищённую транзакцию.

> **Безопасность:** анализ сохранённых данных и отчёты не требуют движения принтера. Measurement может нагревать и перемещать принтер. Запись Input Shaper изменяет `printer.cfg` и runtime-настройки. Эти действия выполняйте только осознанно и при отсутствии активной печати.

## Установка и первый запуск

| Вариант | Когда выбирать | Рабочие данные |
|---|---|---|
| Portable | Запуск без установки, перенос папки, тест новой версии | `runtime` рядом с `K2SpatialAnalyzer.exe` |
| MSI | Обычная Windows-установка и ярлык в меню Пуск | `%LOCALAPPDATA%\FDM_AI_Lab\K2_Spatial_Analyzer\runtime` |

> Для Portable с CUDA используйте короткий ASCII-путь, например `D:\K2Spatial\K2_Spatial_Analyzer\`. Не распаковывайте CUDA-версию в каталог с кириллицей в полном пути.

1. Распакуйте Portable либо установите MSI.
2. Запустите `K2SpatialAnalyzer.exe` или ярлык.
3. Выберите «Русский» или English.
4. Для первого знакомства сначала анализируйте готовый Measurement без подключения принтера.

## Основные вкладки

| Вкладка | Назначение |
|---|---|
| Измерения | Measurement/Belt Gate, live-сбор RAW |
| Анализ | Источник, движки, параметры, численный анализ |
| Результаты | Input Shaper/max_accel, HTML/PDF, защищённая запись shaper |
| Экспорт PNG | Статические графики |
| Сравнение профилей | COLD / LOW_TEMP / HIGH_TEMP |
| Журнал | Команды, прогресс, предупреждения и ошибки |

## Архитектура пользовательского маршрута

| Стадия | Вход | Выход / следующий шаг |
|---|---|---|
| Measurement Gate | Endpoint K2, профиль, Nx/Ny/Nz, belt policy | Разрешающий gate-state + `SPATIAL_MEASUREMENT_REQUEST.json` |
| Spatial acquisition | PASS Gate + request contract | RAW dataset + `SPATIAL_ACQUISITION_RESULT.json` |
| Analyzer | Архив/папка Spatial | ENGINE_RESULTS/COMPARISON, recommendations, zoning, cache, report state |
| Static/reporting | Analyzer output | PNG, HTML, PDF; язык определяется GUI/REPORT_LANGUAGE |
| Profile Comparison | 2–3 независимых профиля | pair summaries, recommendation/envelope deltas, HTML/PDF |
| Input Shaper write | Только K2_NATIVE candidates | Backup → `printer.cfg` mutation → read-back → runtime `SET_INPUT_SHAPER` verify |

## Хранилище runtime и конфигурация

Приложение разделяет неизменяемый binary payload и mutable runtime. Portable определяет режим по `portable.mode` и хранит runtime рядом с EXE; MSI направляет runtime в LocalAppData.

| Каталог | Назначение |
|---|---|
| `runtime/state` | `config.json` и GUI-state |
| `runtime/logs` | Сессионные журналы и startup diagnostics |
| `runtime/measurements` | Measurement Gate, acquisition sessions и готовые Measurement |
| `runtime/analysis` | Analyzer и Profile Comparison |
| `runtime/engines` | Полученные/подготовленные исходники K2 Native и upstream Klipper |
| `runtime/exports`, `cache`, `gate_imports` | Экспорт/кэш/импорт ранее выполненных Gate |

### Ключевые параметры config.json

| Ключ | Назначение / default |
|---|---|
| `language` | `ru` |
| `cuda_enabled` | `false`; сохраняется из GUI |
| `analysis_workers` | `0 = Auto` |
| `printer_endpoint` | пусто до ввода пользователя |
| `moonraker_port` | `7125` |
| `ssh_user` | `root` |
| `ssh_port` | `22` |
| `ssh_key_path` | ссылка на внешний SSH key; ключ не входит в release |
| `last_profile` | `LOW_TEMP` |
| `auto_start_spatial_after_gate` | `false` |

> **Credentials:** приложение хранит только ссылку на SSH key path. Секреты не должны входить в GitHub/release/demo dataset.

## Расчётные движки

| Engine | Что исполняется | Практическая роль |
|---|---|---|
| `K2_NATIVE_EXACT` | Фактические vendor-исходники K2 Plus. K2 Native autotune использует legacy SCV=5 при выборе shaper | Authoritative контур для K2 и единственный разрешённый источник для записи shaper |
| `UPSTREAM_EXACT` | Фактические `shaper_calibrate.py` / `shaper_defs.py` выбранной pinned revision Klipper | Exact-source comparison с современным upstream |
| `cleanroom_upstream_2026` | Независимая реализация современной математики Klipper | Cross-check; не требует внешней папки исходников |

Кнопки «Получить с K2» и «Скачать Klipper» подготавливают соответствующие engine source roots. Если внешний engine не нужен, снимите его галочку; Analyzer требует минимум один выбранный engine.

## Параметры Analyzer

| Параметр | Default | Смысл |
|---|---:|---|
| SCV | 5.0 | Square corner velocity расчётного контура |
| Жёсткий max_accel | 12000 мм/с² | Проектный hard ceiling |
| Шаг округления max_accel | 100 мм/с² | Квантование итоговой рекомендации |
| Размер графиков | 3840×2160 | Static export/reporting |
| Smoothing limit | 0.12 | Reference/ограничивающая величина |
| CPU workers | 0 = Auto | Auto = min(max(logical_cpus − 1, 1), 16); manual clamp ≤ logical CPUs и ≤16 |
| CUDA RAW FFT | Off | CuPy ускоряет только RAW Welch FFT/PSD preprocessing |
| 3D zoning residual threshold | 1.0 % | Порог residual для zoning |
| Зоны ускорений | 3000,4000,5000,6000 | Practical Print Envelopes |
| Zoning engine | UPSTREAM_EXACT | Engine detailed zoning |

> **CPU workers:** ProcessPool используется для RAW preprocessing. Ноль означает Auto, а не «один поток».

> **CUDA:** ускорение не распространяется на exact-engine fitting, Plotly и Matplotlib/PDF, поэтому общий runtime может быть ограничен не GPU-стадией.

## Источник Spatial и контракт dataset

- Archive mode: TAR, TAR.GZ/TGZ или ZIP. ZIP может непосредственно содержать Spatial-папку с RAW либо один TAR/TAR.GZ с такой папкой.
- Folder mode: выбранная папка должна содержать RAW напрямую либо иметь вложенную папку с RAW.
- Partial dataset разрешён. Общий `max_accel` строится только при наличии AX и AY.
- Не используйте 7z/LZMA2 как импортируемый Measurement archive: GUI/worker контракт рассчитан на TAR/TAR.GZ/TGZ/ZIP.

## Measurement Gate — детальная логика

### Профили температуры

| Profile | Target / условие |
|---|---|
| COLD | Пассивный: nozzle/bed/chamber ≤35 °C |
| LOW_TEMP | Nozzle 140 °C · bed 70 °C · chamber 45 °C |
| HIGH_TEMP | Nozzle 280 °C · bed 110 °C · chamber 58 °C |

Если targets уже достигнуты, дополнительный 30-минутный hold не выполняется. Иначе targets устанавливаются, ожидается попадание температур в допуски, затем hold 1800 с. `keep_heat_on_exit=true`.

### Belt / authorization policy

| Условие | STANDARD | UNSTABLE_PRINTER |
|---|---|---|
| Vendor model | `BELT_MDL_TEST target_error=0` по X и Y обязателен | То же |
| Repeatability | Стабильные повторные INFO обязательны | Plateau threshold ослаблен ×1.5, repeatability всё равно проверяется |
| Imbalance ≤3.5% | NOMINAL | Диагностический |
| >3.5…≤5% | REVIEW | Диагностический |
| >5…≤10% | WARNING, авторизация возможна при vendor/stability PASS | Диагностический |
| >10% | До 3 X→Y retension+stress cycles; persistent >10 = FAIL/BLOCK | Не блокирует сам по себе |

### Stress sequence

Precondition: `G28 → Z_TILT_ADJUST → Z=175 → X/Y=175/175`. Stress geometry использует X/Y 20…330 мм при Z=175, периметр/диагонали и три ступени:

| Скорость | Ускорение |
|---:|---:|
| 150 мм/с | 3000 мм/с² |
| 300 мм/с | 6000 мм/с² |
| 600 мм/с | 12000 мм/с² |

После третьей ступени — обязательный settle 180 с, затем финальные 3× `BELT_MDL_INFO` и `BELT_MDL_TEST`. Только после этого Gate принимает решение.

### Spatial grid

- X/Y точки — центры равных ячеек физического объёма 0…350 мм.
- Z — inclusive linspace 20…330 мм.
- Диагонали DPP/DPM измеряются при X=175, Y=175 для каждого Z layer.
- Default GUI grid = 3×3×3. Полный survey содержит 60 RAW основных/диагональных измерений плюс отдельный preflight RAW; для 4×4×3 основной набор — 102 RAW.

> **Plan-only:** «Сформировать план без принтера» использует тот же планирующий контракт, но не запускает live Measurement.

## Acquisition contract и отказоустойчивость

- Acquisition требует `spatial_authorized=true`, согласованный plan SHA и разрешающий gate-state.
- Перед каждым измерением контролируется позиционирование; RAW копируется транзакционно с проверкой стабильности/хеша/длительности.
- При проблеме предусмотрены повторные попытки; аппаратные MCU/LIS2DW ошибки переводят контур в fail-fast.
- Итоговый handoff — `SPATIAL_ACQUISITION_RESULT.json`.

## Forced reevaluation

Forced reevaluation не меняет baseline Analyzer и сама по себе не записывает `printer.cfg`. Она позволяет выбрать для AX/AY отдельные engine/shaper/frequency hypotheses и пересчитать effective-state/reporting.

| Поле | Смысл |
|---|---|
| Вкл. | Включает forced-строку |
| Движок | K2_NATIVE_EXACT / UPSTREAM_EXACT / cleanroom |
| Ось | AX или AY |
| Shaper | zv / mzv / ei / 2hump_ei / 3hump_ei |
| Частота | Candidate frequencies; редактируется вручную |
| Целевое сглаживание | Target smoothing |
| Критерий автовыбора | residual vibration / Worst / smoothing |
| Автовыбор | Лучший рассчитанный candidate по выбранному критерию |

> **Provenance:** forced charts/report должны быть явно маркированы «Forced override». Forced effective state нельзя смешивать с automatic baseline.

## Интерпретация Results

- Smoothing @ 5000 мм/с² — только FDM AI Lab comparison reference, не default Klipper.
- Result smoothing пересчитывается на фактически рекомендованном общем `max_accel`.
- Result Worst и residual vibration относятся к выбранному shaper/frequency и в данном reporting contract от `max_accel` не изменяются.
- Limiter показывает ограничивающую ось/условие.

## Output artifacts

| Файл | Роль |
|---|---|
| `RUN_METADATA.json` | Параметры запуска и provenance |
| `SPATIAL_FREQ_CACHE.npz + INDEX` | Кэш RAW для переоценки |
| `ENGINE_RESULTS.json` | Подробные engine results |
| `ENGINE_COMPARISON.csv` | Candidate/recommended rows |
| `ACCELERATION_RECOMMENDATIONS.csv` | Baseline acceleration recommendations |
| `EFFECTIVE_ENGINE_RESULTS.json` / `EFFECTIVE_ENGINE_COMPARISON.csv` | Effective-state после forced reevaluation |
| `EFFECTIVE_ACCELERATION_RECOMMENDATIONS.csv` | Effective acceleration recommendations |
| `FORCED_SHAPER_OVERRIDES.json` | Provenance forced overrides |
| `ZONING_RESULTS.json` | Zoning/Practical Print Envelopes |
| `REPORT.html` / `REPORT.md` / PDF | Reporting layer |
| `PNG_EXPORT_STATE.json` + `charts\*.png` | Tracked-state статического экспорта |

## Profile Comparison — provenance и reproducibility

- Минимум два профиля COLD / LOW_TEMP / HIGH_TEMP; третий опционален.
- Для каждого слота Archive или Folder. Folder может быть Spatial dataset либо Analyzer output.
- Duplicate-source gate запрещает один canonical source нескольким профилям.
- Profile provenance читается из metadata. Явное несовпадение — blocker; legacy/unknown допускается после предупреждения и подтверждения.
- GUI передаёт workers и CUDA flags в каждый per-profile Analyzer.
- Output включает `PROFILE_PAIR_SUMMARY.csv`, `PROFILE_RECOMMENDATION_DELTAS.csv`, `PROFILE_ENVELOPE_DELTAS.csv`, если данные доступны.

## Static reporting и язык

- После Analyzer numerical critical path завершается с `--no-charts`, затем GUI запускает deterministic static exporter.
- PNG freshness определяется tracked hashes analysis/effective files, не только mtime/size.
- «Обновить из результата» выполняет effective-state → summary → REPORT; PNG регенерируются только если missing/stale.
- `REPORT_LANGUAGE.json` фиксирует ru/en. Technical identifiers намеренно не переводятся.

## Input Shaper write-safety transaction

> Запись разрешена только для рассчитанных `K2_NATIVE_EXACT` candidates. Forced/UPSTREAM/cleanroom сами по себе не являются источником write.

1. На вкладке «Результаты» нажмите «Записать Input Shaper на K2 Plus».
2. Для X/AX и Y/AY выберите Auto либо конкретный рассчитанный candidate.
3. Приложение читает текущий `printer.cfg` и состояние принтера.
4. Blocker: printing/paused; unexpected active `[input_shaper]` в include-файле.
5. Показывается current effective shaper и новые K2_NATIVE значения; askyesno по умолчанию «Нет».
6. После «Да» создаётся и SHA-проверяется `printer.cfg.backup`.
7. SAVE_CONFIG-tail `[input_shaper]` удаляется; предыдущий основной блок comment-disable; новый bilingual FDM AI Lab block вставляется после него.
8. Файл перечитывается и проверяется; runtime применяет `SET_INPUT_SHAPER` с `SHAPER_TYPE_X/Y` и `SHAPER_FREQ_X/Y`.
9. Runtime response верифицируется. `SAVE_CONFIG`, `CXSAVE_CONFIG`, `RESTART`, `FIRMWARE_RESTART` не вызываются.
10. Report сохраняется как `INPUT_SHAPER_WRITE_<timestamp>.json`.

Следующая штатная калибровка Input Shaper может перезаписать значения; GUI предупреждает об этом перед записью.

## CLI / internal worker reference

Обычному пользователю CLI не требуется. Для воспроизводимой диагностики GUI соответствует параметрам:

```text
--source <archive|folder>
--output <runtime analysis dir>
--scv 5.0
--printer-hard-cap 12000
--accel-round-step 100
--chart-width 3840 --chart-height 2160
--quality-residual-pct 1.0
--zone-thresholds 3000,4000,5000,6000
--zoning-engine UPSTREAM_EXACT
--workers 0
--cuda
--k2-native-source ...
--upstream-source ...
```

В production GUI worker запускается через единый `K2SpatialAnalyzer.exe --internal-worker ...`, а не внешний `python.exe`.

### 3D ViewPort

ViewPort не является частью обычного UI V1.0.0. Он оставлен только для разработки и запускается source/dev флагом `--enable-viewport`. Не документируйте его как пользовательскую release-функцию.

## Диагностика

| Маркер / симптом | Интерпретация |
|---|---|
| `ANALYZER_STATUS=PASS` | Численный Analyzer завершён успешно |
| `DATASET_STATUS=FULL_DYNAMIC_102` | Dataset распознан как полный динамический набор |
| `RAW_RECOGNIZED=N` | Количество распознанных survey RAW |
| `ZONING_STATUS=PASS` | Zoning построен |
| `CUDA_REQUESTED=1, CUDA_ACTIVE=1` | GPU backend реально активен |
| CUDA path could not be detected | Не blocker сам по себе; смотрите CUDA_ACTIVE и итоговый статус |
| `PROFILE_SOURCE_DUPLICATE` | Один source назначен нескольким profile slots |
| Profile provenance mismatch | Metadata доказывает другой профиль |
| `FROZEN_CHILD_FATAL` | Ошибка child worker до штатного main; используйте session/startup log |

## Recommended acceptance sequence для новой машины

1. Запуск приложения и RU↔EN.
2. CUDA self-test или CPU route.
3. Анализ известного сохранённого Measurement.
4. HTML/PDF/PNG reporting.
5. Profile Comparison на двух независимых dataset, если доступны.
6. Plan-only Measurement.
7. Только затем — осознанный live Measurement на исправном K2 Plus.
8. Write-safety transaction — отдельным финальным gate после review рассчитанных K2_NATIVE candidates.
