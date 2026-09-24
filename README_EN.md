# K2 Spatial Analyzer — User Guide

**Standard edition · V1.0.0 · 23 September 2026**

Step-by-step guide for regular users, from first launch to reports.

[Русский](README_RU.md) · [Advanced EN](README_ADVANCED_EN.md) · [Advanced RU](README_ADVANCED_RU.md)

> This documentation is intended for publication on GitHub. Examples do not use real IP addresses, usernames, local paths, or other private data.

## How to use this guide

The standard edition is for users who need to launch the ready-to-use Windows package, analyze a Measurement, obtain recommendations and reports, without going into the application's architecture. Dangerous or rarely used functions are marked separately.

> **Important:** “Standard” and “Advanced” are two documentation editions, not two application modes. K2 Spatial Analyzer has one common interface.

## What K2 Spatial Analyzer can do

- accept a Spatial Measurement as TAR/TAR.GZ/TGZ/ZIP or a folder containing RAW data;
- calculate Input Shaper recommendations and an overall recommended `max_accel` from available AX/AY measurements;
- use `K2_NATIVE_EXACT`, `cleanroom_upstream_2026`, and `UPSTREAM_EXACT` calculation engines;
- optionally accelerate RAW Welch FFT/PSD with CuPy/CUDA; CPU analysis remains available when CUDA is unavailable;
- generate HTML/PDF/PNG reports and compare COLD / LOW_TEMP / HIGH_TEMP profiles;
- run Measurement/Belt Gate and spatial RAW acquisition on K2 Plus;
- write a calculated K2_NATIVE Input Shaper to K2 Plus through a separate protected transaction.

> **Safety:** analysis of saved data and report generation do not require printer movement. Measurement may heat and move the printer. Input Shaper write changes `printer.cfg` and runtime settings. Perform these operations deliberately and only when no print is active.

## Installation and first launch

### Portable or MSI

| Variant | When to choose it | Working data |
|---|---|---|
| Portable | Run without installation, move the whole folder, test a new version | `runtime` beside `K2SpatialAnalyzer.exe` |
| MSI | Normal Windows installation and Start-menu shortcut | `%LOCALAPPDATA%\FDM_AI_Lab\K2_Spatial_Analyzer\runtime` |

> **CUDA path:** for Portable with CUDA, use an ASCII-only path such as `D:\K2Spatial\K2_Spatial_Analyzer\`. Do not unpack the CUDA build into a path containing Cyrillic characters.

1. Portable: unpack the archive into a separate folder with a short ASCII path. MSI: run the installer and complete the normal Windows installation.
2. Start `K2SpatialAnalyzer.exe` or the “K2 Spatial Analyzer” shortcut.
3. Select Русский or English at the bottom of the window. Language changes without restarting the application.
4. For your first session, do not connect the printer. Analyze a saved Measurement first.

## Main tabs

| Tab | Purpose |
|---|---|
| Measurement | Measurement/Belt Gate and live RAW acquisition from K2 Plus |
| Analysis | Select source, engines and parameters; run numerical analysis |
| Results | Input Shaper/max_accel summary, HTML/PDF, protected shaper write |
| PNG Export | Generate and inspect static plots |
| Profile Comparison | Compare two or three COLD / LOW_TEMP / HIGH_TEMP profiles |
| Log | Commands, progress, warnings and child-operation errors |

## Quick start: analyze a saved Measurement

> **Recommended first workflow:** if you already have a Measurement, start with it. This completely separates learning the Analyzer from live printer control.

5. Open **Analysis**.
6. Select source type: TAR/TAR.GZ/ZIP archive or Spatial report folder.
7. Browse to the Measurement. A folder must contain RAW directly or contain a nested folder with RAW.
8. For the first run, leave `cleanroom_upstream_2026` enabled. Enable `K2_NATIVE_EXACT` and `UPSTREAM_EXACT` only when their source roots are available.
9. Leave CPU workers at 0 (Auto). If a supported NVIDIA/CuPy environment is available, enable **CUDA RAW FFT (CuPy)** and wait for the CUDA check.
10. Do not change SCV, hard max_accel, zoning, or other numerical parameters until you understand the basic workflow.
11. Click **Validate input**. After PASS, click **Run analysis**.
12. The application opens the Log tab during calculation. After successful numerical analysis, static PNG files and the report are generated automatically.
13. Open **Results**, review the recommendations, and generate a PDF if needed.

> **Partial dataset:** a dataset does not have to contain the full standard measurement set. Analyzer uses the available AX/AY points. Overall `max_accel` is produced only when both primary axes are present.

## Reading Results

| Field | Meaning |
|---|---|
| Input Shaper X / Y | Recommended shaper type and frequency for the axis |
| Worst X / Y, % | Worst metric for the selected result; lower is better within the comparison |
| Smoothing X / Y @ 5000 | FDM AI Lab comparison reference at 5000 mm/s²; not a “Klipper standard” |
| X / Y limit | Calculated acceleration limit for each axis |
| Limiter | Axis/condition limiting the overall result |
| Result Worst / smoothing / residual vibration | Metrics for the selected result; Result smoothing is recalculated for overall recommended max_accel |
| Recommended max_accel | Overall recommended limit when both primary axes are available |

### HTML, PDF and PNG

- **Open HTML report** — convenient interactive/on-screen result view.
- **Generate PDF** — creates a PDF in the currently selected UI language.
- **PNG Export** — generates static plots; preview supports 5–400% zoom and Fit to window.
- **Refresh from result** — synchronizes effective-state, summary and REPORT; current PNG files are not regenerated unnecessarily.

## CUDA: what it accelerates

CUDA in V1.0.0 accelerates only RAW Welch FFT/PSD preprocessing. Exact-engine fitting, Plotly and Matplotlib/PDF are not accelerated by this option.

A successful CUDA route includes:

```text
CUDA_REQUESTED=1
CUDA_ACTIVE=1
CUDA_BACKEND=CUPY_ACTIVE:<GPU>
ANALYZER_STATUS=PASS
```

> A “CUDA path could not be detected” warning is not an error by itself. If `CUDA_ACTIVE=1` and `ANALYZER_STATUS=PASS`, the CuPy backend is actually active.

## Comparing COLD / LOW_TEMP / HIGH_TEMP

14. Open **Profile Comparison**. Each profile has separate Archive and Folder selectors.
15. Select at least two different sources. The same source cannot be assigned to two profile slots.
16. If metadata explicitly proves the profile, the application will not allow LOW_TEMP to be assigned as COLD or HIGH_TEMP.
17. If a legacy dataset lacks provable profile provenance, the application warns and offers to continue as legacy/unknown without guessing.
18. Run profile comparison. After PASS, open the comparison HTML or generate PDF.

**Compared data:** matching XYZ/axis points, Input Shaper changes, residual, max_accel and Practical Print Envelopes. Temperature slope is marked observational, not causal.

## Measurement on a real K2 Plus

> **Do not start here.** First verify that analysis of a saved Measurement works. Live Measurement includes thermal/belt/stress gates and physical printer motion.

19. On **Measurement**, enter the printer IP/hostname and run the connection test.
20. Select COLD, LOW_TEMP or HIGH_TEMP.
21. For the first session, leave the grid at 3×3×3 and use **Generate plan without printer** first.
22. Before live execution, make sure no print is active, the working volume is clear, and the printer is healthy.
23. Run the mandatory Measurement Gate. It checks temperature profile, belt model/repeatability, and performs the stress sequence.
24. After Gate PASS, Spatial acquisition can start. If automatic start is enabled, acquisition begins automatically after authorization.
25. The finished Measurement appears in `runtime\measurements` and can then be used in Analysis.

| Profile | Temperature mode |
|---|---|
| COLD | Passive: nozzle ≤35 °C, bed ≤35 °C, chamber ≤35 °C |
| LOW_TEMP | Nozzle 140 °C, bed 70 °C, chamber 45 °C |
| HIGH_TEMP | Nozzle 280 °C, bed 110 °C, chamber 58 °C |

> **Heating:** if profile targets are already reached, no additional 30-minute hold is required. Otherwise targets are reached and held for 1800 s. Heating is not automatically disabled after completion.

## Writing Input Shaper to K2 Plus

> **Rarely needed by beginners:** you can use Analyzer without this function. It modifies printer configuration.

- Write is available only from calculated `K2_NATIVE_EXACT` results.
- X/AX and Y/AY are selected independently: Auto or a calculated candidate.
- Before modification, the application reads current `printer.cfg` and blocks writing while printing/paused or when an unexpected active `[input_shaper]` exists in an include file.
- A verified `printer.cfg` backup is created before writing; SAVE_CONFIG `[input_shaper]` is cleared, the old main block is commented, and the new FDM AI Lab block is written after it.
- Runtime is applied with `SET_INPUT_SHAPER` without `SAVE_CONFIG`, `CXSAVE_CONFIG`, or restart.
- Final confirmation defaults to **No**. Cancel if unsure.

## Where files and logs are stored

| Data | Portable | MSI |
|---|---|---|
| Logs | `<program folder>\runtime\logs` | `%LOCALAPPDATA%\FDM_AI_Lab\K2_Spatial_Analyzer\runtime\logs` |
| Measurement | `<program folder>\runtime\measurements` | `…\runtime\measurements` |
| Analysis results | `<program folder>\runtime\analysis` | `…\runtime\analysis` |
| State/settings | `<program folder>\runtime\state` | `…\runtime\state` |

## Common problems

| Symptom | Check |
|---|---|
| Spatial source not found | Selected file/folder and source type |
| RAW not found | You may have selected the wrong folder level |
| K2_NATIVE_EXACT / UPSTREAM_EXACT does not start | The corresponding source root is required; otherwise disable it and use cleanroom |
| CUDA_PATH warning | Check actual `CUDA_ACTIVE`; with `CUDA_ACTIVE=1` it is not a backend failure |
| Comparison rejects source | Check duplicate assignment and provenance |
| PDF language is wrong | Switch GUI language and generate PDF again |
| Operation error | Open Log, then `runtime\logs`; keep the source Measurement until diagnostics are complete |

## Sharing a Measurement safely

- Do not publish original session/startup/fatal/build logs.
- Before publication, inspect JSON/TXT/LOG/MD for IP/hostname, usernames, absolute paths, serial/MAC, tokens, keys and other secrets.
- Keep RAW CSV byte-identical where possible if textual headers contain no private data.
- For a demo Measurement use ordinary ZIP/Deflate; after sanitation rerun Analyzer and verify RAW count and zoning.

## Issue checklist

Include:
- application version and Portable/MSI;
- workflow: Analysis / Profile Comparison / Measurement / Write;
- only neutral log lines without IP, usernames or local paths;
- `ANALYZER_STATUS`, `CUDA_ACTIVE`, `DATASET_STATUS`, `RAW_RECOGNIZED`, `ZONING_STATUS` when applicable.

Do **not** attach credentials, private `printer.cfg`, or unsanitized Measurement/logs.

## Glossary

| Term | Meaning |
|---|---|
| Measurement | Spatial set of RAW vibration measurements and metadata |
| RAW | Sensor source data for a specific point/axis |
| AX / AY | Primary X and Y measurement axes |
| Input Shaper | Shaper type and frequency combination used to suppress resonances |
| SCV | Square corner velocity parameter used by the calculation route |
| Zoning | Practical Print Envelopes constructed for selected acceleration thresholds |
| Forced reevaluation | Reevaluate calculated candidates without changing baseline or writing `printer.cfg` |
| K2_NATIVE_EXACT | Executes actual K2 Plus vendor sources |
| UPSTREAM_EXACT | Executes actual pinned Klipper source |
| cleanroom_upstream_2026 | Independent implementation of modern Klipper mathematics for cross-checking |
