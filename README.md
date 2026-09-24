# Creality K2 Plus Spatial Input Shaper Analyzer

Windows application by **FDM AI Lab** for spatial Input Shaper measurement, analysis, profile comparison, reporting, and protected K2 Plus shaper configuration.

> **V1.0.0 / GEN17.4 — first public beta candidate.** Use live Measurement and configuration-write functions only when you understand their physical effects on the printer.

## Download

Binary packages are distributed through **GitHub Releases**, not through the repository source tree.

- **Portable x64 (.7z)** — no installation; unpack to a short ASCII-only path.
- **Windows x64 (.msi)** — standard Windows installation.
- Demo Measurement may be provided separately as a sanitized ZIP.

The Portable archive is a distribution container only. Do **not** use 7z as a Measurement import format: Measurement import supports TAR/TAR.GZ/TGZ/ZIP.

## Documentation

| Language | Standard guide | Advanced guide |
|---|---|---|
| English | [README_EN.md](README_EN.md) | [README_ADVANCED_EN.md](README_ADVANCED_EN.md) |
| Русский | [README_RU.md](README_RU.md) | [README_ADVANCED_RU.md](README_ADVANCED_RU.md) |

The Standard guide covers installation, saved-Measurement analysis, CUDA, profile comparison, live Measurement, safe Input Shaper write, troubleshooting, and issue preparation. The Advanced guide documents engine behavior, Measurement Gate, dataset contracts, artifacts, forced reevaluation, diagnostics, and write-safety transaction.

## Main capabilities

- Spatial Measurement analysis from TAR/TAR.GZ/TGZ/ZIP or RAW folder.
- Input Shaper and overall `max_accel` recommendations from AX/AY data.
- `K2_NATIVE_EXACT`, `UPSTREAM_EXACT`, and `cleanroom_upstream_2026` calculation routes.
- Optional CuPy/CUDA acceleration for RAW Welch FFT/PSD preprocessing.
- HTML/PDF/PNG reports.
- COLD / LOW_TEMP / HIGH_TEMP profile comparison.
- Measurement/Belt Gate and spatial acquisition on Creality K2 Plus.
- Protected K2_NATIVE Input Shaper write transaction.

## Safety

Saved-data analysis and report generation do not move the printer.

**Live Measurement can heat and move the printer. Input Shaper write modifies `printer.cfg` and runtime settings.** Do not run these operations during an active print. Review the documentation and use plan-only Measurement before the first live test.

## Privacy when reporting issues

Do not publish credentials, SSH keys, private `printer.cfg`, unsanitized runtime logs, IP/hostname, usernames, local paths, serial/MAC data, or private Measurement metadata. See the documentation for the issue checklist and Measurement sanitation guidance.

## Release status

V1.0.0 / GEN17.4 is the first working public beta candidate. Portable has been manually tested in the project validation contour. MSI should be treated as requiring broader user acceptance testing.

Please report reproducible problems through GitHub Issues using sanitized diagnostic information.
