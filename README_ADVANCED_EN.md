# K2 Spatial Analyzer — Advanced User Guide

**Advanced edition · V1.0.0 · 23 September 2026**

Extended guide covering parameters, engines, Measurement Gate, artifacts, diagnostics, and write safety.

[Standard EN](README_EN.md) · [Русский Advanced](README_ADVANCED_RU.md)

> This documentation is intended for publication on GitHub. Examples do not use real IP addresses, usernames, local paths, or other private data.

## How to use this guide

The Advanced edition is for users who want to understand not only the GUI workflow but also the data, checks, and algorithmic paths behind each operation. It is also intended for diagnostics, reproducible testing, and preparing useful GitHub issues.

> **Important:** Standard and Advanced are documentation editions, not application modes. K2 Spatial Analyzer has one common interface.

## Capabilities

K2 Spatial Analyzer can:

- accept Spatial Measurement as TAR/TAR.GZ/TGZ/ZIP or a folder containing RAW data;
- calculate Input Shaper recommendations and overall recommended `max_accel` from available AX/AY measurements;
- use `K2_NATIVE_EXACT`, `cleanroom_upstream_2026`, and `UPSTREAM_EXACT`;
- optionally accelerate RAW Welch FFT/PSD using CuPy/CUDA, with CPU fallback;
- generate HTML/PDF/PNG reports and COLD / LOW_TEMP / HIGH_TEMP comparison;
- perform Measurement/Belt Gate and spatial RAW acquisition on K2 Plus;
- write calculated K2_NATIVE Input Shaper through a separate protected transaction.

> **Safety:** saved-data analysis and reporting do not require printer motion. Measurement may heat and move the printer. Input Shaper write modifies `printer.cfg` and runtime settings. Perform live operations only deliberately and when no print is active.

## Installation and runtime storage

| Variant | Use case | Working data |
|---|---|---|
| Portable | No installation, movable folder, testing | `runtime` beside `K2SpatialAnalyzer.exe` |
| MSI | Standard Windows installation | `%LOCALAPPDATA%\FDM_AI_Lab\K2_Spatial_Analyzer\runtime` |

For CUDA Portable, use a short ASCII-only path such as `D:\K2Spatial\K2_Spatial_Analyzer\`.

### Main tabs

| Tab | Purpose |
|---|---|
| Measurement | Measurement/Belt Gate, live RAW acquisition |
| Analysis | Source, engines, parameters, numerical analysis |
| Results | Input Shaper/max_accel, HTML/PDF, protected shaper write |
| PNG Export | Static plots |
| Profile Comparison | COLD / LOW_TEMP / HIGH_TEMP comparison |
| Log | Commands, progress, warnings and child-operation errors |

## User workflow architecture

| Stage | Input | Output / next step |
|---|---|---|
| Measurement Gate | K2 endpoint, profile, Nx/Ny/Nz, belt policy | Authorizing gate-state + `SPATIAL_MEASUREMENT_REQUEST.json` |
| Spatial acquisition | PASS Gate + request contract | RAW dataset + `SPATIAL_ACQUISITION_RESULT.json` |
| Analyzer | Spatial archive/folder | ENGINE_RESULTS/COMPARISON, recommendations, zoning, cache, report state |
| Static/reporting | Analyzer output | PNG, HTML, PDF; language follows GUI/REPORT_LANGUAGE |
| Profile Comparison | 2–3 independent profiles | pair summaries, recommendation/envelope deltas, HTML/PDF |
| Input Shaper write | K2_NATIVE candidates only | Backup → `printer.cfg` mutation → read-back → runtime `SET_INPUT_SHAPER` verify |

## Runtime and configuration

The application separates immutable binary payload from mutable runtime. Portable mode is selected by `portable.mode` and stores runtime beside the EXE. MSI uses LocalAppData.

| Directory | Purpose |
|---|---|
| `runtime/state` | `config.json` and GUI state |
| `runtime/logs` | Session logs and startup diagnostics |
| `runtime/measurements` | Measurement Gate, acquisition sessions, completed Measurements |
| `runtime/analysis` | Analyzer and Profile Comparison results |
| `runtime/engines` | Prepared K2 Native and upstream Klipper sources |
| `runtime/exports`, `cache`, `gate_imports` | Export, cache, imported previous Gate state |

### Key config.json parameters

| Key | Default / meaning |
|---|---|
| `language` | `ru` |
| `cuda_enabled` | `false`; persisted from GUI |
| `analysis_workers` | `0 = Auto` |
| `printer_endpoint` | empty until entered |
| `moonraker_port` | `7125` |
| `ssh_user` | `root` |
| `ssh_port` | `22` |
| `ssh_key_path` | reference to external SSH key; key is not part of release |
| `last_profile` | `LOW_TEMP` |
| `auto_start_spatial_after_gate` | `false` |

> **Credentials:** the application stores only a reference to an SSH key path. Secrets must not be included in GitHub, release packages, or demo datasets.

## Calculation engines

| Engine | Execution | Practical role |
|---|---|---|
| `K2_NATIVE_EXACT` | Actual K2 Plus vendor sources; K2 Native autotune uses legacy SCV=5 when selecting a shaper | Authoritative K2 route and the only allowed source for shaper write |
| `UPSTREAM_EXACT` | Actual `shaper_calibrate.py` / `shaper_defs.py` from selected pinned Klipper revision | Exact-source comparison with modern upstream |
| `cleanroom_upstream_2026` | Independent implementation of modern Klipper mathematics | Cross-check; no external source folder required |

**Get from K2** and **Download Klipper** prepare the corresponding engine source roots. Analyzer requires at least one selected engine.

## Analyzer parameters

| Parameter | Default | Meaning |
|---|---:|---|
| SCV | 5.0 | Square corner velocity for calculation |
| Hard max_accel | 12000 mm/s² | Project hard ceiling |
| max_accel rounding step | 100 mm/s² | Final recommendation quantization |
| Chart size | 3840×2160 | Static export/reporting |
| Smoothing limit | 0.12 | Reference/limiting value |
| CPU workers | 0 = Auto | Auto = min(max(logical_cpus − 1, 1), 16); manual clamp ≤ logical CPUs and ≤16 |
| CUDA RAW FFT | Off | CuPy accelerates only RAW Welch FFT/PSD preprocessing |
| 3D zoning residual threshold | 1.0% | Zoning residual threshold |
| Acceleration zones | 3000,4000,5000,6000 | Practical Print Envelope thresholds |
| Zoning engine | UPSTREAM_EXACT | Engine used for detailed zoning |

> **CPU workers:** ProcessPool is used for RAW preprocessing. Zero means Auto, not one thread.

> **CUDA:** CUDA does not accelerate exact-engine fitting, Plotly, or Matplotlib/PDF, so total runtime may be limited by non-GPU stages.

## Spatial source and dataset contract

- Archive mode: TAR, TAR.GZ/TGZ, or ZIP. ZIP may contain the Spatial RAW folder directly or one TAR/TAR.GZ containing it.
- Folder mode: selected folder must contain RAW directly or a nested folder containing RAW.
- Partial datasets are allowed. Overall `max_accel` requires both primary AX and AY.
- Do not use 7z/LZMA2 as an imported Measurement archive; the GUI/worker contract supports TAR/TAR.GZ/TGZ/ZIP.

## Measurement Gate

### Temperature profiles

| Profile | Target / condition |
|---|---|
| COLD | Passive: nozzle/bed/chamber ≤35 °C |
| LOW_TEMP | Nozzle 140 °C · bed 70 °C · chamber 45 °C |
| HIGH_TEMP | Nozzle 280 °C · bed 110 °C · chamber 58 °C |

If targets are already reached, the additional 30-minute hold is skipped. Otherwise targets are set, temperature tolerances are reached, then held for 1800 s. `keep_heat_on_exit=true`.

### Belt / authorization policy

| Condition | STANDARD | UNSTABLE_PRINTER |
|---|---|---|
| Vendor model | `BELT_MDL_TEST target_error=0` on X and Y required | Same |
| Repeatability | Stable repeated INFO required | Plateau threshold relaxed ×1.5; repeatability still checked |
| Imbalance ≤3.5% | NOMINAL | Diagnostic |
| >3.5…≤5% | REVIEW | Diagnostic |
| >5…≤10% | WARNING; authorization possible with vendor/stability PASS | Diagnostic |
| >10% | Up to 3 X→Y retension+stress cycles; persistent >10 = FAIL/BLOCK | Does not block by itself |

### Stress sequence

Precondition: `G28 → Z_TILT_ADJUST → Z=175 → X/Y=175/175`. Stress geometry uses the physical X/Y range 20…330 mm at Z=175, perimeter/diagonals, and three stages:

| Speed | Acceleration |
|---:|---:|
| 150 mm/s | 3000 mm/s² |
| 300 mm/s | 6000 mm/s² |
| 600 mm/s | 12000 mm/s² |

After stage 3, mandatory settle is 180 s, followed by final 3× `BELT_MDL_INFO` and `BELT_MDL_TEST`. Gate decides only after this sequence.

### Spatial grid

- X/Y points are centers of equal cells in the physical 0…350 mm volume.
- Z is inclusive linspace 20…330 mm.
- DPP/DPM diagonals are measured at X=175, Y=175 for every Z layer.
- Default GUI grid is 3×3×3. A full survey contains 60 main/diagonal RAW measurements plus a separate preflight RAW; 4×4×3 contains 102 main RAW measurements.

> **Plan-only:** **Generate plan without printer** uses the same planning contract without starting live Measurement.

## Acquisition contract and fault tolerance

- Acquisition requires `spatial_authorized=true`, matching plan SHA, and an authorizing gate-state.
- Positioning is checked before every measurement; RAW is copied transactionally with stability/hash/duration validation.
- RAW acquisition retries are available; MCU/LIS2DW hardware errors are fail-fast.
- Final acquisition handoff is `SPATIAL_ACQUISITION_RESULT.json`.

## Forced reevaluation

Forced reevaluation does not change baseline Analyzer and does not itself write `printer.cfg`. It lets you select separate engine/shaper/frequency hypotheses for AX and AY and recalculate effective-state/reporting.

| Field | Meaning |
|---|---|
| Enabled | Enables the forced row |
| Engine | K2_NATIVE_EXACT / UPSTREAM_EXACT / cleanroom |
| Axis | AX or AY |
| Shaper | zv / mzv / ei / 2hump_ei / 3hump_ei |
| Frequency | Candidate frequencies, still manually editable |
| Target smoothing | Smoothing target for reevaluation |
| Auto-selection criterion | residual vibration / Worst / smoothing |
| Auto-select | Best calculated candidate for selected criterion |

> **Provenance:** forced charts/reports must be explicitly marked **Forced override**. Do not mix forced effective state with automatic baseline.

## Results interpretation

- Smoothing @ 5000 mm/s² is an FDM AI Lab comparison reference, not a default Klipper value.
- Result smoothing is recalculated at the actual recommended overall `max_accel`.
- Result Worst and residual vibration refer to the selected shaper/frequency and do not change with `max_accel` in this reporting contract.
- Limiter identifies the axis/condition limiting overall `max_accel`.

## Output artifacts

| File | Role |
|---|---|
| `RUN_METADATA.json` | Run parameters and Measurement/profile provenance |
| `SPATIAL_FREQ_CACHE.npz + INDEX` | Frequency-domain RAW cache |
| `ENGINE_RESULTS.json` | Detailed engine results |
| `ENGINE_COMPARISON.csv` | Candidate/recommended rows |
| `ACCELERATION_RECOMMENDATIONS.csv` | Baseline acceleration recommendations |
| `EFFECTIVE_ENGINE_RESULTS.json` / `EFFECTIVE_ENGINE_COMPARISON.csv` | Effective-state after forced reevaluation |
| `EFFECTIVE_ACCELERATION_RECOMMENDATIONS.csv` | Effective acceleration recommendations |
| `FORCED_SHAPER_OVERRIDES.json` | Explicit forced-override provenance |
| `ZONING_RESULTS.json` | Zoning/Practical Print Envelopes |
| `REPORT.html` / `REPORT.md` / PDF | Human-readable reporting |
| `PNG_EXPORT_STATE.json` + `charts\*.png` | Tracked static graphics state |

## Profile Comparison provenance and reproducibility

- At least two profiles from COLD / LOW_TEMP / HIGH_TEMP; third is optional.
- Each slot can use Archive or Folder. Folder may be a Spatial dataset or completed Analyzer output.
- Duplicate-source gate prevents one canonical source from being assigned to multiple profiles.
- Profile provenance is read from metadata. Explicit mismatch is a blocker; legacy/unknown is allowed only after warning and user confirmation.
- GUI passes current workers and CUDA flags into each per-profile Analyzer.
- Output includes `PROFILE_PAIR_SUMMARY.csv`, `PROFILE_RECOMMENDATION_DELTAS.csv`, and `PROFILE_ENVELOPE_DELTAS.csv` where data is available.

## Static reporting and language

- After successful Analyzer, the numerical critical path finishes with `--no-charts`, then GUI starts deterministic static exporter.
- PNG freshness uses tracked hashes of analysis/effective files, not only mtime/size.
- **Refresh from result** performs effective-state → summary → REPORT; PNG files regenerate only when missing/stale.
- `REPORT_LANGUAGE.json` stores selected ru/en. Technical identifiers intentionally remain untranslated.

## Input Shaper write-safety transaction

> Write is allowed only from calculated `K2_NATIVE_EXACT` candidates. Forced/UPSTREAM/cleanroom are not write sources by themselves.

1. In Results, choose **Write Input Shaper to K2 Plus**.
2. For X/AX and Y/AY choose Auto or a calculated candidate.
3. Application reads current `printer.cfg` and printer state.
4. Blockers: printing/paused; unexpected active `[input_shaper]` in an include file.
5. Current effective shaper and new K2_NATIVE values are shown; confirmation defaults to No.
6. After explicit Yes, `printer.cfg.backup` is created and hash-verified.
7. SAVE_CONFIG-tail `[input_shaper]` is removed; previous main block is comment-disabled; new bilingual FDM AI Lab block is inserted after it.
8. File is read back and verified; runtime applies `SET_INPUT_SHAPER` with `SHAPER_TYPE_X/Y` and `SHAPER_FREQ_X/Y`.
9. Runtime response is verified. `SAVE_CONFIG`, `CXSAVE_CONFIG`, `RESTART`, and `FIRMWARE_RESTART` are not invoked.
10. Transaction report is saved as `INPUT_SHAPER_WRITE_<timestamp>.json`.

A later normal Input Shaper calibration may overwrite these values; GUI warns about this before write.

## CLI / internal worker reference

Regular users do not need CLI. For reproducible diagnostics, GUI maps to:

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

In production, GUI starts the worker through the single `K2SpatialAnalyzer.exe --internal-worker ...`, not an external `python.exe`.

### 3D ViewPort

ViewPort is not part of the normal V1.0.0 UI. It is development-only and enabled with source/dev flag `--enable-viewport`. Do not document it as a normal release feature.

## Diagnostics

| Marker / symptom | Interpretation |
|---|---|
| `ANALYZER_STATUS=PASS` | Numerical Analyzer completed successfully |
| `DATASET_STATUS=FULL_DYNAMIC_102` | Dataset recognized as full dynamic set of corresponding size |
| `RAW_RECOGNIZED=N` | Number of recognized survey RAW files |
| `ZONING_STATUS=PASS` | Zoning built successfully |
| `CUDA_REQUESTED=1, CUDA_ACTIVE=1` | GPU backend actually active |
| CUDA path could not be detected | Not a blocker by itself; check CUDA_ACTIVE and final status |
| `PROFILE_SOURCE_DUPLICATE` | Same source assigned to multiple profile slots |
| Profile provenance mismatch | Metadata proves a different profile |
| `FROZEN_CHILD_FATAL` | Child worker failed before normal main; use session/startup log for diagnostics |

## Privacy / GitHub publication policy

- Do not attach unsanitized runtime logs to issues/releases; they may contain endpoints and absolute local paths.
- Do not publish SSH keys, tokens, cookies, printer credentials, private configs, Google Drive IDs, or machine-specific network metadata.
- Build demo Measurements from an allowlist of required RAW/metadata rather than copying everything and deleting obvious private files.
- Keep RAW CSV byte-identical when textual headers contain no private data. After sanitation, rerun Analyzer and verify RAW_RECOGNIZED and zoning.
- Use ZIP/Deflate, not 7z/LZMA2, for demo Measurement import containers.

## Recommended acceptance sequence for a new machine

1. Launch application and test RU↔EN.
2. Run CUDA self-test or CPU route.
3. Analyze a known saved Measurement.
4. Verify HTML/PDF/PNG reporting.
5. Run Profile Comparison on two independent datasets if available.
6. Run plan-only Measurement.
7. Only then run deliberate live Measurement on a healthy K2 Plus.
8. Treat write-safety transaction as a separate final gate after reviewing calculated K2_NATIVE candidates.
