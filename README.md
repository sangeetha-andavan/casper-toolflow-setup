# CASPER Toolflow Setup

Installation and troubleshooting notes for the CASPER FPGA toolflow and `casperfpga`, focused on the Xilinx ZCU216 RFSoC.

The main setup described here uses Ubuntu 20.04, MATLAB R2021a Update 8, Vivado/Vitis Model Composer 2021.1, and Python 3.8.10. Check the [CASPER documentation](https://casper-toolflow.readthedocs.io) before adapting the commands to another release.

## Guides

1. [Complete environment setup](docs/00-complete-toolflow-environment-setup.md)
2. [Version compatibility](docs/01-version-matrix-and-scope.md)
3. [Toolflow installation](docs/02-toolflow-installation.md)
4. [`casperfpga` and ZCU216 bring-up](docs/03-casperfpga-and-board-bringup.md)
5. [Troubleshooting](docs/04-troubleshooting.md)
6. [References](docs/05-references.md)

## Configuration

| Component | Version / setting |
|---|---|
| Ubuntu | 20.04 LTS |
| MATLAB | R2021a Update 8 |
| Vivado / Vitis Model Composer | 2021.1 |
| Python | 3.8.10 virtual environment |
| `mlib_devel` | `m2021a` branch |
| `casperfpga` | `py38` branch |
| Jasper backend | Vitis for the documented `.dtbo` workflow |
| Board | ZCU216; confirm the silicon variant for your hardware |

The guides cover installation, `startsg.local`, `.fpg` and `.dtbo` generation, board communication, firmware programming, register tests, and common errors.

## Safety

- Verify the SD-card device with `lsblk` before writing an image; a wrong `dd` target can erase another disk.
- Back up `startsg.local` before editing it.
- Avoid changing vendor libraries, system symlinks, or `/bin/sh` unless the cause is understood and you can revert the change.
- Do not commit credentials, licence files, private network details, or proprietary installers.
