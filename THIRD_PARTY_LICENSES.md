# Third-Party License Inventory

Release line: **K2 Spatial Analyzer V1.0.0 / GEN17.4**  
Target: **Windows x64 / canonical CUDA13 build**

This repository copy is a release inventory derived from the canonical locked build definition `requirements-release-win64-cuda13.lock.txt`. During the actual Windows build, the frozen application payload independently generates its own `THIRD_PARTY_LICENSES.md` using:

`pip-licenses --format=markdown --with-urls --with-license-file`

That generated file travels inside the Portable/MSI application payload and contains the package-provided license-file contents. Package license files and upstream license terms remain authoritative.

## Locked Python package inventory

| Package | Version | License / license family |
|---|---:|---|
| pip | 26.2.1 | MIT |
| Pillow | 12.3.0 | MIT-CMU |
| bcrypt | 5.0.0 | Apache-2.0 |
| bottle | 0.13.4 | MIT |
| cffi | 2.1.1 | MIT-0 |
| charset-normalizer | 3.5.1 | MIT |
| clr_loader | 0.3.1 | MIT |
| contourpy | 1.4.0 | BSD-3-Clause |
| cryptography | 50.0.1 | Apache-2.0 OR BSD-3-Clause |
| customtkinter | 6.0.0 | MIT |
| cycler | 0.12.1 | BSD-3-Clause |
| darkdetect | 0.8.0 | BSD-3-Clause |
| fonttools | 4.65.0 | MIT |
| invoke | 3.0.3 | BSD-2-Clause |
| kiwisolver | 1.5.1 | BSD |
| matplotlib | 3.11.2 | Matplotlib license / PSF-compatible |
| narwhals | 2.26.0 | MIT |
| numpy | 2.5.3 | BSD-3-Clause plus bundled third-party notices |
| packaging | 26.3 | Apache-2.0 OR BSD-2-Clause |
| paramiko | 5.0.0 | LGPL-2.1 |
| plotly | 7.1.0 | MIT |
| proxy_tools | 0.1.0 | BSD |
| pycparser | 3.0 | BSD-3-Clause |
| pynacl | 1.6.2 | Apache-2.0 |
| pyparsing | 3.3.2 | MIT |
| python-dateutil | 2.9.0.post0 | Apache-2.0 OR BSD-3-Clause |
| pythonnet | 3.1.0 | MIT |
| pywebview | 6.2.1 | BSD-3-Clause |
| reportlab | 5.0.1 | BSD |
| six | 1.17.0 | MIT |
| typing_extensions | 4.16.0 | PSF-2.0 |
| PyInstaller | 6.22.3 | GPL-2.0-or-later with PyInstaller bootloader exception; selected files Apache-2.0 |
| pyinstaller-hooks-contrib | 2026.7 | GPL-2.0 / Apache-2.0 |
| altgraph | 0.17.5 | MIT |
| pefile | 2024.8.26 | MIT |
| pywin32-ctypes | 0.2.3 | BSD-3-Clause |
| setuptools | 84.0.0 | MIT |
| pip-licenses | 5.5.5 | MIT |
| prettytable | 3.18.0 | BSD-3-Clause |
| wcwidth | 0.8.4 | MIT |
| cuda-pathfinder | 1.8.2 | Apache-2.0 |
| cuda-toolkit | 13.4.2 | NVIDIA CUDA Toolkit package; governed by NVIDIA CUDA licensing |
| cupy-cuda13x[ctk] | 14.2.0 | MIT |
| nvidia-cublas | 13.8.0.4 | LicenseRef-NVIDIA-Proprietary |
| nvidia-cuda-nvrtc | 13.4.92 | LicenseRef-NVIDIA-Proprietary |
| nvidia-cuda-runtime | 13.4.92 | LicenseRef-NVIDIA-Proprietary |
| nvidia-cufft | 12.4.0.43 | LicenseRef-NVIDIA-Proprietary |
| nvidia-curand | 10.4.4.72 | LicenseRef-NVIDIA-Proprietary |
| nvidia-cusolver | 12.3.4.7 | LicenseRef-NVIDIA-Proprietary |
| nvidia-cusparse | 12.8.6.72 | LicenseRef-NVIDIA-Proprietary |
| nvidia-nvjitlink | 13.4.92 | LicenseRef-NVIDIA-Proprietary |

## External source engine

K2 Spatial Analyzer may fetch selected files from **Klipper** commit `84104bbbd7625d28b4eaaad83f4562972460ecd9` at runtime.

- Upstream: https://github.com/Klipper3d/klipper
- License: GNU GPLv3
- License text: https://www.gnu.org/licenses/gpl-3.0.html
- Distribution model in GEN17.4: Klipper source is not bundled in the application package; selected source files are fetched on demand.

## License references

- Apache License 2.0: https://www.apache.org/licenses/LICENSE-2.0
- BSD 2-Clause: https://opensource.org/license/bsd-2-clause
- BSD 3-Clause: https://opensource.org/license/bsd-3-clause
- GNU GPLv2: https://www.gnu.org/licenses/old-licenses/gpl-2.0.html
- GNU GPLv3: https://www.gnu.org/licenses/gpl-3.0.html
- GNU LGPLv2.1: https://www.gnu.org/licenses/old-licenses/lgpl-2.1.html
- MIT License: https://opensource.org/license/mit
- Python Software Foundation License: https://docs.python.org/3/license.html
- NVIDIA CUDA EULA: https://docs.nvidia.com/cuda/eula/

## Scope note

This inventory covers the canonical locked GEN17.4 Windows x64/CUDA13 Python environment plus the externally fetched Klipper exact-source engine. It is not a license grant for K2 Spatial Analyzer itself. The package-generated license file embedded in the binary distribution remains the authoritative record of license-file contents for the installed Python packages.
