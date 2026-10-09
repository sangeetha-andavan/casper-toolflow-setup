# 1. Version compatibility and scope

## 1.1 Why version matching matters

The CASPER toolflow is sensitive to version mismatches. Before installing anything, select a compatible row from the [official CASPER toolflow installation page](https://casper-toolflow.readthedocs.io/en/latest/src/Installing-the-Toolflow.html). That page lists ZCU216 combinations including Ubuntu 20.04, Python 3.8, MATLAB R2021a or R2022a, and Vivado 2021.1 or 2023.1 with `m2021a` or `m2022a`, respectively. Re-check the live page because the matrix can change.

## 1.2 Configuration recorded in the supplied project documents

| Component | Recorded configuration |
|---|---|
| OS | Ubuntu 20.04 LTS |
| MATLAB | R2021a Update 8 |
| Vivado | 2021.1 ML Enterprise |
| Vitis Model Composer | 2021.1 |
| Python | 3.8 virtual environment |
| Toolflow repository | `casper-astro/mlib_devel`, branch `m2021a` |
| Python control library | `casper-astro/casperfpga`, branch `py38` |
| Device-tree repository | `Xilinx/device-tree-xlnx`, branch recorded as `xlnx_rel_v2021.1` |
| FPGA target | ZCU216 production silicon |

**Important branch note:** the device-tree branch is recorded as `xlnx_rel_v2021.1` in the supplied notes. Other official tutorials may use a different release branch for a different tool version. Do not assume the branch name is universal; use the official RFSoC tutorial and the branch matching your exact installed release.

## 1.3 Separate the three software layers

1. **`casperfpga`** — Python library used to connect to a running board, upload firmware, and access registers. It is not the full FPGA build toolchain.
2. **`mlib_devel` / Jasper toolflow** — CASPER Simulink libraries and build scripts used to compile FPGA designs.
3. **Vendor design tools and platform support** — MATLAB/Simulink, Vivado, Vitis Model Composer, licences, FPGA part definitions, and (for the Vitis RFSoC backend) a compatible device-tree repository.

Installing one layer does not prove that the others are installed or configured.

## 1.4 Before you begin

- Confirm you have permission and valid licences for MATLAB, Vivado, and Model Composer.
- Confirm the exact ZCU216 silicon variant (engineering sample versus production).
- Record installer filenames, versions, repository branches, and commit hashes.
- Use an isolated Python 3.8 environment for this recorded setup.
- Keep a copy of the original `startsg.local.example` and any local configuration before editing.

## Official references

- [CASPER Toolflow installation and compatibility matrix](https://casper-toolflow.readthedocs.io/en/latest/src/Installing-the-Toolflow.html)
- [CASPER Toolflow documentation](https://casper-toolflow.readthedocs.io/en/latest/)
- [CASPER `mlib_devel` repository, `m2021a` branch](https://github.com/casper-astro/mlib_devel/tree/m2021a)
- [CASPER RFSoC getting-started tutorial](https://casper-toolflow.readthedocs.io/projects/tutorials/en/latest/tutorials/rfsoc/tut_getting_started.html)
