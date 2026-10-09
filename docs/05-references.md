# 5. References and upstream resources

The two project documents supplied for this repository describe a specific ZCU216 setup and several observed failures. Use official documentation to check version compatibility before applying any workaround.

## Core CASPER documentation

1. **Toolflow installation and compatibility matrix**  
   https://casper-toolflow.readthedocs.io/en/latest/src/Installing-the-Toolflow.html  
   Use this first to choose compatible OS, MATLAB, Xilinx tools, branch, and Python versions.

2. **Install `casperfpga`**  
   https://casper-toolflow.readthedocs.io/en/latest/src/How-to-install-casperfpga.html  
   Covers the Python 3.8 environment and the `py38` branch workflow.

3. **CASPER Toolflow documentation**  
   https://casper-toolflow.readthedocs.io/en/latest/

4. **Official RFSoC getting-started tutorial**  
   https://casper-toolflow.readthedocs.io/projects/tutorials/en/latest/tutorials/rfsoc/tut_getting_started.html  
   Includes RFSoC setup and Vitis/device-tree configuration.

5. **CASPER `mlib_devel`, `m2021a` branch**  
   https://github.com/casper-astro/mlib_devel/tree/m2021a

6. **CASPER `casperfpga` repository**  
   https://github.com/casper-astro/casperfpga

7. **CASPER `casperfpga` installation guide in source repository**  
   https://github.com/casper-astro/casperfpga/blob/py38/docs/How-to-install-casperfpga.rst

8. **Vivado installation notes for `m2021a`**  
   https://github.com/casper-astro/mlib_devel/blob/m2021a/docs/src/How-to-install-Xilinx-Vivado.rst

## RFSoC device-tree backend

9. **CASPER mailing-list archive: Vitis/device-tree discussion**  
   https://www.mail-archive.com/casper@lists.berkeley.edu/msg09031.html

10. **Xilinx device-tree repository**  
    https://github.com/Xilinx/device-tree-xlnx  
    Choose the release branch matching the Vivado/Vitis version used by the selected tutorial. Do not assume `master` or a branch name copied from another version is compatible.

11. **StrathSDR System Generator notes for Ubuntu 20.04 / Vivado 2021**  
    https://strath-sdr.github.io/tools/matlab/sysgen/vivado/linux/2021/01/28/sysgen-on-20-04.html  
    A third-party troubleshooting reference; compare it with current upstream guidance before changing system libraries.

## Hardware and platform image

12. **AMD/Xilinx ZCU216 product and documentation page**  
    https://www.amd.com/en/products/adaptive-socs-and-fpgas/evaluation-boards/zcu216.html  
    Consult the relevant board user guide for switch settings, connectors, and hardware-specific procedures.

## How to report a useful issue

When searching upstream issues or asking the CASPER community for help, include:
- Exact error text and the full relevant log (remove secrets and private paths).
- Ubuntu release and kernel.
- MATLAB, Vivado, Vitis Model Composer and Vitis versions.
- `mlib_devel` branch and commit hash.
- `casperfpga` branch and commit hash.
- Target board and whether it is engineering-sample or production silicon.
- Relevant `startsg.local` settings with personal paths and sensitive details redacted.
- What you tried and the first point where the procedure diverged from the official guide.

Do not report “installation failed” alone; a minimal, reproducible error report makes the issue much easier to diagnose.
