# K2 Spatial Analyzer V1.0.0 / GEN17.4

## English

First public beta candidate of **K2 Spatial Analyzer** for Creality K2 Plus.

The application provides spatial Input Shaper measurement and analysis,
K2/upstream engine comparison, optional CUDA RAW preprocessing,
static and interactive reporting, temperature-profile comparison,
and a protected K2_NATIVE Input Shaper write workflow.

### Downloads

**K2_Spatial_Analyzer_V1.0.0_Portable_x64.7z**  
Portable Windows x64 package. No installation required.  
Extract to a short ASCII-only path.

**K2_Spatial_Analyzer_V1.0.0_x64.msi**  
Windows x64 installer.

### Recommended first run

Start with the Portable package and a saved Measurement.
Do not connect the printer for the first analysis.
After confirming that analysis and reporting work, review the
documentation before using live Measurement or Input Shaper write.

### Documentation

- `README_EN.md` — English User Guide
- `README_ADVANCED_EN.md` — English Advanced Guide
- `README_RU.md` — Russian User Guide
- `README_ADVANCED_RU.md` — Russian Advanced Guide
- `THIRD_PARTY_NOTICES.md` — third-party notices
- `THIRD_PARTY_LICENSES.md` — third-party license inventory

### Important safety information

Saved Measurement analysis and report generation do not require printer motion.

**Live Measurement may heat and move the Creality K2 Plus.
Input Shaper write modifies `printer.cfg` and runtime settings.**

Never run these operations during an active print.
Use plan-only Measurement before the first live run.

### CUDA

CUDA acceleration is optional and applies to RAW Welch FFT/PSD preprocessing.
Exact-engine fitting and report rendering are not GPU-accelerated.
CPU operation remains available.

For Portable CUDA use a short ASCII-only extraction path,
for example `C:\K2Spatial\K2_Spatial_Analyzer\`.

### Beta status

- Portable: project validation and manual acceptance routes completed.
- MSI: provided for beta testing; broader installation acceptance is requested.
- Real printer Measurement and configuration-write operations should be treated
  as advanced functions and performed deliberately.

Please report reproducible problems through GitHub Issues.

**Sanitize diagnostic data before posting:** do not include credentials,
SSH keys, private `printer.cfg`, IP/hostname, usernames, absolute local paths,
serial/MAC information, or unsanitized runtime logs/Measurement metadata.

### Portable archive integrity note

The Portable distribution may be repacked into `.7z` after the application
build to reduce download size. Therefore the final public `.7z` is a
post-build distribution artifact and its SHA-256 must be calculated from
the exact uploaded `.7z` file. A hash from the pre-repack build package
is not expected to match it.

---

## Русский

Первая публичная бета-версия **K2 Spatial Analyzer** для Creality K2 Plus.

Приложение предназначено для пространственных измерений и анализа
Input Shaper, сравнения результатов движков K2/upstream, опциональной
CUDA-обработки RAW-данных, формирования статических и интерактивных
отчётов, сравнения температурных профилей и защищённой записи
Input Shaper в режиме K2_NATIVE.

### Загрузка

**K2_Spatial_Analyzer_V1.0.0_Portable_x64.7z**  
Портативная версия для Windows x64. Установка не требуется.  
Распакуйте архив в короткий путь, содержащий только ASCII-символы.

**K2_Spatial_Analyzer_V1.0.0_x64.msi**  
Установщик для Windows x64.

### Рекомендуемый первый запуск

Начните с Portable-версии и ранее сохранённого Measurement.
Для первого анализа подключение к принтеру не требуется.

После проверки анализа и формирования отчётов ознакомьтесь с
документацией перед использованием живого Measurement или записи
Input Shaper в конфигурацию принтера.

### Документация

- `README_EN.md` — руководство пользователя на английском
- `README_ADVANCED_EN.md` — расширенное руководство на английском
- `README_RU.md` — руководство пользователя на русском
- `README_ADVANCED_RU.md` — расширенное руководство на русском
- `THIRD_PARTY_NOTICES.md` — сведения о сторонних компонентах
- `THIRD_PARTY_LICENSES.md` — перечень лицензий сторонних компонентов

### Важная информация по безопасности

Анализ сохранённого Measurement и формирование отчётов
не требуют движения принтера.

**Live Measurement может выполнять нагрев и перемещения Creality K2 Plus.
Запись Input Shaper изменяет `printer.cfg` и runtime-настройки принтера.**

Не запускайте эти операции во время печати.
Перед первым реальным Measurement используйте режим plan-only.

### CUDA

CUDA-ускорение является опциональным и применяется к предварительной
обработке RAW-данных Welch FFT/PSD.

Расчёты exact-engine и формирование отчётов GPU не ускоряются.
Работа полностью в CPU-режиме сохраняется.

Для Portable-версии с CUDA используйте короткий путь только
с ASCII-символами, например:

`C:\K2Spatial\K2_Spatial_Analyzer\`

### Статус бета-версии

- Portable: пройдены проектные проверки и предусмотренные маршруты ручной приёмки.
- MSI: предоставляется для бета-тестирования; требуется расширенная проверка установки.
- Реальные Measurement и операции записи конфигурации принтера следует
  рассматривать как расширенные функции и запускать осознанно.

Воспроизводимые ошибки можно сообщать через GitHub Issues.

**Перед публикацией диагностических данных обязательно удаляйте приватную
информацию:** пароли и другие учётные данные, SSH-ключи, приватный
`printer.cfg`, IP-адрес/hostname, имена пользователей, абсолютные локальные
пути, серийные номера/MAC и несанированные runtime-логи или метаданные
Measurement.

### Примечание о целостности Portable-архива

После сборки приложения Portable-дистрибутив может быть повторно упакован
в `.7z` для уменьшения размера загрузки.

Поэтому публичный `.7z` является отдельным post-build артефактом,
и его SHA-256 должен рассчитываться непосредственно по тому файлу `.7z`,
который загружен в GitHub Release.

SHA-256 исходного build-пакета после такой перепаковки совпадать
с хешем публичного `.7z` не обязан.