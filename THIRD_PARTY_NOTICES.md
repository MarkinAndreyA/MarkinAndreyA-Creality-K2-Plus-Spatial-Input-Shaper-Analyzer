# Third-Party Notices

K2 Spatial Analyzer V1.0.0 / GEN17.4 uses third-party software. Copyright in those components remains with their respective authors and contributors.

This file documents the components and external engines identified in the canonical GEN17.4 source/build definition. The Windows x64 release build also generates a package-level `THIRD_PARTY_LICENSES.md` inside the frozen application payload with `pip-licenses --with-urls --with-license-file`.

## Direct runtime dependencies

| Component | Role | Upstream | License |
|---|---|---|---|
| NumPy | Numerical arrays and signal-processing support | https://numpy.org/ | BSD/permissive; see package license files |
| Matplotlib | Static plotting | https://matplotlib.org/ | Matplotlib/PSF-compatible license |
| CustomTkinter | Desktop GUI widgets | https://github.com/TomSchimansky/CustomTkinter | MIT |
| Pillow | Image processing | https://python-pillow.github.io/ | MIT-CMU |
| ReportLab | PDF generation | https://www.reportlab.com/opensource/ | BSD |
| Plotly.py | Interactive plotting | https://github.com/plotly/plotly.py | MIT |
| pywebview | Embedded HTML/WebView UI | https://github.com/r0x0r/pywebview | BSD-3-Clause |
| Paramiko | SSH transport | https://www.paramiko.org/ | LGPL-2.1 |
| CuPy | Optional CUDA RAW FFT/PSD preprocessing | https://cupy.dev/ | MIT |

## Build and packaging components

The canonical Windows release environment also contains PyInstaller, pyinstaller-hooks-contrib, altgraph, pefile, pywin32-ctypes, setuptools and pip-licenses. PyInstaller 6.22.3 is distributed under GPL-2.0-or-later with the PyInstaller bootloader exception; some PyInstaller files are Apache-2.0. The exception permits distribution of applications built with PyInstaller under other licenses, subject to the licenses of the bundled dependencies.

The exact locked Windows x64/CUDA13 package inventory and license identifiers are recorded in [THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md).

## Klipper exact-source engine

K2 Spatial Analyzer can fetch selected source files from the upstream Klipper project at the pinned commit:

`84104bbbd7625d28b4eaaad83f4562972460ecd9`

Upstream: https://github.com/Klipper3d/klipper

Klipper is licensed under GNU GPLv3. The Klipper source tree is not vendored into this repository or the GEN17.4 application payload. The selected upstream source is fetched on demand and executed through the Analyzer's isolated exact-source worker.

GNU GPLv3: https://www.gnu.org/licenses/gpl-3.0.html

## NVIDIA CUDA components

The canonical CUDA13 Windows build uses NVIDIA CUDA packages and runtime libraries. NVIDIA CUDA runtime components are not ordinary open-source dependencies; redistribution is governed by the NVIDIA Software License Agreement and CUDA Toolkit Supplement, including the distributable-components list in Attachment A.

NVIDIA CUDA EULA: https://docs.nvidia.com/cuda/eula/

The locked build uses `cuda-pathfinder` (Apache-2.0), `cupy-cuda13x` (MIT), and NVIDIA CUDA runtime/library packages identified as `LicenseRef-NVIDIA-Proprietary` in current PyPI metadata.

## No project-wide license grant

These notices describe third-party components only. They do not grant a license to K2 Spatial Analyzer source code, documentation, branding, or other first-party FDM AI Lab material.

For exact package versions and license identifiers used by the GEN17.4 Windows x64/CUDA13 build, see [THIRD_PARTY_LICENSES.md](THIRD_PARTY_LICENSES.md).
