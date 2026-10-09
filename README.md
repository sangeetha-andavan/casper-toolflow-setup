# CASPER Toolflow Setup and Troubleshooting

Practical setup notes for the CASPER FPGA toolflow and `casperfpga`, with a focus on the **Xilinx ZCU216 RFSoC**. This repository organises a working setup record, board bring-up steps, common failures, recovery notes, and links to official documentation.

> **Important:** CASPER toolflow builds depend on a compatible combination of Ubuntu, MATLAB/Simulink, Vivado/Vitis Model Composer, Python, `mlib_devel`, platform files, and device-tree sources. Check the official compatibility matrix before copying any version-specific command. The configuration recorded here is a historical, working project configuration—not a promise that every installation will behave identically.

## Start here

1. [Choose compatible versions](docs/01-version-matrix-and-scope.md)
2. [Install the CASPER toolflow](docs/02-toolflow-installation.md)
3. [Install `casperfpga`](docs/03-casperfpga-and-board-bringup.md)
4. [Troubleshoot errors](docs/04-troubleshooting.md)
5. [References and upstream issue resources](docs/05-references.md)

## Project-tested configuration recorded in the source notes

| Component | Recorded version / setting |
|---|---|
| Host OS | Ubuntu 20.04 LTS |
| MATLAB | R2021a Update 8 |
| Vivado | 2021.1, ML Enterprise |
| Vitis Model Composer | 2021.1 |
| Python | 3.8 in a virtual environment |
| `mlib_devel` | `m2021a` branch |
| `casperfpga` | `py38` branch |
| Target | ZCU216 production silicon |
| RFSoC backend | Jasper with Vitis backend for `.dtbo` generation |

The uploaded project notes report a successful build of a ZCU216 tutorial platform, `.fpg` generation, and later `.dtbo` generation after configuring the Vitis backend and matching device-tree repository. Treat this as a record of that machine's result; record exact commits and installer versions when reproducing it.

## What this repository covers

- Version matching and separation of the Python control library from the FPGA build toolchain.
- Python virtual environment and `casperfpga` installation.
- `mlib_devel` checkout and `startsg.local` configuration.
- ZCU216 SD-card image, serial console, Ethernet, connection, and register tests.
- Reported errors involving `bdist_wheel`, Vivado installation stalls, MATLAB/Simulink library conflicts, missing FPGA part definitions, and missing `XLNX_DT_REPO_PATH`.
- Safe recovery and shutdown notes for a ZCU216 SD card.

## Safety notes

- **Never copy a `dd` command with `/dev/sdb` unchanged.** Device names vary and the wrong target can erase your computer's disk. Identify the SD card with `lsblk`, verify size/model, unmount its partitions, and double-check the target before writing.
- Do not run Xilinx installers as root unless the installer documentation for that exact package says to. The source notes conflict on this point for Vivado versus Vitis; follow the vendor installer instructions consistently.
- Do not move vendor libraries, create system-wide symlinks, or change `/bin/sh` without understanding the system-wide consequences. These are legacy workarounds, not universal first-line fixes.
- Back up `startsg.local` before editing it. Do not commit machine-specific paths, IP addresses, credentials, licence files, or proprietary installers.
- The default board credentials recorded in some notes should be changed or secured according to your lab's policy.

## Source and confidence labels

Each troubleshooting entry distinguishes:
- **Observed in project notes** — documented in the two supplied setup PDFs.
- **Official reference** — supported by upstream documentation or the vendor/project source.
- **Caution / verify locally** — version-specific or potentially destructive advice that should not be applied blindly.

## Contributing

When adding a fix, include the exact error message, OS/tool versions, hardware revision, the steps tried, the final verified fix, and a link to an upstream issue or mailing-list discussion if available. Never publish passwords, licence details, private network data, or proprietary binaries.
