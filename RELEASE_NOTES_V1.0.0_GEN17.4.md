# K2 Spatial Analyzer V1.0.0 / GEN17.4

First public beta candidate of **K2 Spatial Analyzer** for Creality K2 Plus.

The application provides spatial Input Shaper measurement and analysis, K2/upstream engine comparison, optional CUDA RAW preprocessing, static and interactive reporting, temperature-profile comparison, and a protected K2_NATIVE Input Shaper write workflow.

## Downloads

Attach the release binaries below this text as **GitHub Release assets**:

- `K2_Spatial_Analyzer_V1.0.0_Portable_x64.7z` — Portable Windows x64 package. No installation required. Extract to a short ASCII-only path.
- `K2_Spatial_Analyzer_V1.0.0_x64.msi` — Windows x64 installer.

If a sanitized demo dataset is included:
- `K2_SPATIAL_MEASUREMENT_LOW_TEMP_DEMO.zip` — example Measurement for learning the analysis workflow without running live printer measurement.

## Recommended first run

Start with the Portable package and a saved/demo Measurement. Do not connect the printer for the first analysis. After confirming that analysis and reporting work, review the documentation before using live Measurement or Input Shaper write.

## Documentation

- [English User Guide](README_EN.md)
- [English Advanced Guide](README_ADVANCED_EN.md)
- [Русское руководство](README_RU.md)
- [Расширенное руководство на русском](README_ADVANCED_RU.md)

## Important safety information

Saved Measurement analysis and report generation do not require printer motion.

**Live Measurement may heat and move the Creality K2 Plus. Input Shaper write modifies `printer.cfg` and runtime settings.** Never run these operations during an active print. Use plan-only Measurement before the first live run.

## CUDA

CUDA acceleration is optional and applies to RAW Welch FFT/PSD preprocessing. Exact-engine fitting and report rendering are not GPU-accelerated. CPU operation remains available.

For Portable CUDA use a short ASCII-only extraction path, for example `D:\K2Spatial\K2_Spatial_Analyzer\`.

## Beta status

- Portable: project validation and manual acceptance routes completed.
- MSI: provided for beta testing; broader installation acceptance is requested.
- Real printer Measurement and configuration-write operations should be treated as advanced functions and performed deliberately.

Please report reproducible problems through GitHub Issues. **Sanitize diagnostic data before posting:** do not include credentials, SSH keys, private `printer.cfg`, IP/hostname, usernames, absolute local paths, serial/MAC information, or unsanitized runtime logs/Measurement metadata.

## Portable archive integrity note

The Portable distribution may be repacked into `.7z` after the application build to reduce download size. Therefore the final public `.7z` is a **post-build distribution artifact** and its SHA-256 must be calculated from the exact uploaded `.7z` file. A hash from the pre-repack build package is not expected to match it.
