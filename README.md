# CASPER Toolflow Setup and Troubleshooting

Practical setup and troubleshooting documentation for the CASPER FPGA toolflow and `casperfpga`, focused on the Xilinx ZCU216 RFSoC platform.

CASPER builds depend on compatible versions of Ubuntu, MATLAB/Simulink, Vivado/Vitis Model Composer, Python, `mlib_devel`, platform files, and device-tree sources. Check the [official compatibility matrix](https://casper-toolflow.readthedocs.io/en/latest/src/Installing-the-Toolflow.html) before following version-specific commands.

## Documentation

1. [Complete environment setup](docs/00-complete-toolflow-environment-setup.md)
2. [Version compatibility and scope](docs/01-version-matrix-and-scope.md)
3. [Toolflow installation](docs/02-toolflow-installation.md)
4. [`casperfpga` and ZCU216 board bring-up](docs/03-casperfpga-and-board-bringup.md)
5. [Troubleshooting](docs/04-troubleshooting.md)
6. [References](docs/05-references.md)

## Configuration covered

| Component | Version / setting |
|---|---|
| Host OS | Ubuntu 20.04 LTS |
| MATLAB | R2021a Update 8 |
| Vivado | 2021.1 |
| Vitis Model Composer / Vitis | 2021.1 |
| Python | 3.8 virtual environment |
| `mlib_devel` | `m2021a` branch |
| `casperfpga` | `py38` branch |
| Target | Xilinx ZCU216 RFSoC; confirm the silicon variant for your hardware |
| Jasper backend | Vitis for the documented `.dtbo` generation workflow |

The guides cover installation, environment configuration, FPGA build outputs, board communication, firmware programming, register testing, and common setup failures. Commands and platform settings may need adjustment for different software releases or hardware variants.

## Safety notes

- Before writing an SD-card image, identify the correct whole-device path with `lsblk`. A mistaken target can erase another disk.
- Follow the vendor installation instructions for your exact Xilinx software release.
- Avoid changing vendor libraries, creating system-wide symlinks, or changing `/bin/sh` unless the cause is understood and a rollback plan is available.
- Back up `startsg.local` before editing it.
- Do not commit credentials, licence files, private network details, or proprietary installers.

## Contributing

When documenting a fix, include the error message, OS and tool versions, hardware variant, steps to reproduce, and the resolution. Redact secrets and private infrastructure details before publishing logs.
