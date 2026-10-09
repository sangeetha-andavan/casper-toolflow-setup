# Complete CASPER Toolflow Environment Setup

This guide consolidates the environment setup, installation choices, debugging notes, and Vitis backend configuration for the CASPER RFSoC toolflow described in the project setup document dated 29 January 2026. The recorded target is the Xilinx ZCU216.

> **Version-specific guide:** These instructions describe an Ubuntu 20.04 / MATLAB R2021a / Vivado 2021.1 setup. Check the official CASPER compatibility documentation before adapting them to another release.

## 1. Target hardware and software

| Component | Version or setting in the setup notes |
|---|---|
| Operating system | Ubuntu 20.04 LTS |
| MATLAB | R2021a Update 8 |
| Vivado | 2021.1 |
| Vitis Model Composer | 2021.1 |
| Python | 3.8 virtual environment |
| CASPER toolflow | `mlib_devel`, branch `m2021a` |
| Target board | ZCU216, production silicon |

The setup notes describe starting from a clean system installation and compiling the Jasper backend for an RFSoC platform.

## 2. Clone the CASPER toolflow

```bash
git clone -b m2021a https://github.com/casper-astro/mlib_devel.git
cd mlib_devel
```

Create and activate a Python 3.8 virtual environment, then install the branch's requirements:

```bash
python3.8 -m venv venv
source venv/bin/activate
python -m pip install --upgrade pip setuptools wheel
python -m pip install -r requirements.txt
```

The extra upgrade command prepares common Python build tools; if a dependency is pinned to older tooling, follow the dependency and branch requirements rather than forcing a newer version.

Create the local startup configuration from the example:

```bash
cp startsg.local.example startsg.local
```

Configure paths in `startsg.local` for MATLAB R2021a, Vivado 2021.1, Model Composer 2021.1, and the Python virtual environment. Keep local paths specific to the machine and do not commit personal paths or secrets.

## 3. MATLAB toolbox selection

The setup document records installing only:

- MATLAB
- Simulink
- DSP System Toolbox
- Signal Processing Toolbox
- Fixed-Point Designer

The notes state that additional toolboxes were avoided because of System Generator initialization or compilation conflicts, and specifically caution against MATLAB Compiler and MATLAB Compiler SDK in that installation. Treat this as an observed configuration for this toolchain, not a universal rule for all CASPER releases or designs. Confirm required products against the supported workflow before installing or removing toolboxes.

### GTK module warning

The setup notes describe a MATLAB launch message indicating that `canberra-gtk-module` failed to load. They list these legacy commands:

```bash
sudo apt-get install 'libcanberra-gtk*' libgconf-2-4
```

The source also proposes a manual symbolic link from the GTK module location to `/usr/lib/libcanberra-gtk-module.so`. Do not create a system-wide symlink blindly: first confirm the module exists, check the exact error and architecture, and prefer the package-managed fix for the installed Ubuntu release. A warning alone may not mean MATLAB has failed to start.

## 4. Install Vivado and Vitis Model Composer

The setup used Xilinx Vivado ML Enterprise 2021.1, Vitis HLS, and Vitis Model Composer 2021.1.

The original setup notes give this example for launching the downloaded Unified Installer from the Downloads directory:

```bash
cd ~/Downloads
chmod +x Xilinx_Unified_2021.1_0610_2318_Lin64.bin
./Xilinx_Unified_2021.1_0610_2318_Lin64.bin
```

The exact installer filename depends on the downloaded package. The notes specify running the Vivado GUI installer without `sudo`.

Select the products recorded in the setup notes:

- Vivado Design Suite
- Vivado ML Enterprise (not Standard)
- Vitis HLS
- Vitis Model Composer

Under device support, the notes say to select:

- SoCs
- Zynq UltraScale
- Zynq UltraScale+
- Engineering Sample Devices, if ES1 or related engineering-sample parts are required

**Hardware-specific caution:** the setup notes later describe a production-silicon ZCU216 whose platform file selected an engineering-sample part. Select device support for the actual board and toolflow platform. Do not assume that enabling ES devices or changing a part string is required for every ZCU216.

## 5. Common installation problems

### 5.1 Vivado installer stalls at “Generating installed devices list”

The setup document records an installer stall at this stage and suggests installing `libtinfo5` after cancelling the stalled installer:

```bash
sudo apt-get install libtinfo5
```

This is a historical workaround from the setup notes. Before cancelling an installer, verify that it is actually stalled, inspect available disk space and installer logs, and check whether the package is appropriate for the host release. Retry according to the vendor's guidance.

### 5.2 MATLAB / Simulink error involving libhogweed and libgmp

The setup notes include this error:

```text
Caught "std::exception" Exception message is:
Error loading /usr/local/MATLAB/R2021a/bin/glnxa64/
builtins/sl_main/mwlibmwsimulink_builtinimpl.so.
/lib/x86_64-linux-gnu/libhogweed.so.5:
undefined symbol: __gmpn_cnd_sub_n
```

The notes attribute it to a Xilinx-shipped `libgmp.so` shadowing the system library. The historical workaround moved matching libraries into an `exclude` directory in Vivado and Model Composer library directories.

This modifies vendor installations and can break software or be undone by updates. Before considering it, inspect `LD_LIBRARY_PATH`, confirm which libraries MATLAB loads, try a clean shell without custom library-path overrides, and back up the installation. Only change bundled libraries after confirming the conflict for the exact installation and having a rollback plan.

### 5.3 Header include symlink commands

The original notes also list commands to create symbolic links under `/usr/include` for `asm`, `sys`, `bits`, and `gnu`. These are system-wide changes and may be inappropriate on Ubuntu 20.04 or on other distributions. They are preserved here as a record of the original notes, **not as recommended default commands**. Diagnose the actual missing-header error and install the appropriate development packages instead of creating links blindly.

### 5.4 dash versus bash

The original notes suggest:

```bash
sudo dpkg-reconfigure dash
```

and selecting “No” to use bash as `/bin/sh`. This changes system-wide shell behaviour and should not be a routine fix. Only consider it if the exact supported workflow requires it and the consequences for other system scripts are understood.

### 5.5 Legacy Qt4 requirement

The source notes list a historical Qt4 PPA and packages:

```bash
sudo add-apt-repository ppa:rock-core/qt4
sudo apt update
sudo apt install libqtcore4 libqtgui4
```

These packages and repositories are legacy and may be unavailable, unsupported, or unsafe for a current system. Do not add this PPA as a general setup step. First establish whether the specific application actually requires Qt4 and seek a supported dependency path.

## 6. Engineering-sample versus production silicon

The setup notes record this Vivado diagnostic:

```text
Part not found in Vivado installation:
xczu49dr-2-e-es1
```

The documented cause was a mismatch between the ZCU216 platform file's engineering-sample part and the installed Vivado device support on a production-silicon board.

The notes identify this platform file:

```text
mlib_devel/jasper_library/platforms/zcu216.yaml
```

They record changing the FPGA part from:

```text
xczu49dr-ffvf1760-2-e-es1
```

to:

```text
xczu49dr-ffvf1760-2-e
```

Only make such a change after confirming the physical board variant and the matching Vivado part support. The setup notes also suggest installing Engineering Sample Devices when the hardware needs them. These are alternative paths depending on the actual board; they must not be mixed without verification.

## 7. Recorded toolflow build result

After reinstalling Vivado, MATLAB, and Model Composer with the appropriate device support, the setup notes record successful compilation of:

- `zcu216_tut_platform.slx`
- The backend through Jasper
- The bitstream

This section records the result described by the setup document; a specific build should be repeated and checked when reproducing the environment on another machine.

## 8. Enable .dtbo generation with the Vitis backend

### 8.1 Problem

The setup notes describe running `jasper` and getting an `.fpg` file but no `.dtbo`, with this error:

```text
RuntimeError: The environment variable 'XLNX_DT_REPO_PATH' is not on the path.
```

The notes distinguish the outputs as follows:

- `.fpg` — FPGA programming file.
- `.dtbo` — device-tree overlay for the embedded ARM/Linux side of the RFSoC platform.

### 8.2 Select the Vitis backend

The notes say older tutorials may use:

```bash
export JASPER_BACKEND=vivado
```

For the documented RFSoC setup, they use:

```bash
export JASPER_BACKEND=vitis
```

Put the setting in `startsg.local`, not inside the main `startsg` script.

### 8.3 Install the matching Vitis release

The recorded installation paths are:

```text
/tools/Xilinx/Vivado/2021.1
/tools/Xilinx/Vitis/2021.1
```

The setup notes emphasise matching Vivado 2021.1, Vitis 2021.1, and the corresponding device-tree release.

### 8.4 Configure startsg.local

The source document gives the following settings:

```bash
source /tools/Xilinx/Vivado/2021.1/settings64.sh
source /tools/Xilinx/Vitis/2021.1/settings64.sh

export JASPER_BACKEND=vitis
export XLNX_DT_REPO_PATH="$HOME/sandbox/xilinx/device-tree-xlnx"
```

Adjust paths to match the installation. The source document explicitly notes that these lines belong in `startsg.local`, not in `startsg`.

### 8.5 Clone and select the device-tree repository branch

The notes use:

```bash
mkdir -p ~/sandbox/xilinx
cd ~/sandbox/xilinx
git clone https://github.com/Xilinx/device-tree-xlnx.git
cd device-tree-xlnx
git branch -a
git checkout xlnx_rel_v2021.1
git branch
```

The document states that the default `master` branch was incompatible with the recorded Vivado/Vitis 2021.1 setup, and specifies `xlnx_rel_v2021.1`. Confirm that the branch exists and is appropriate for the installed release; use the release-matched branch for any different tool version.

### 8.6 Verify the environment and rebuild

From the CASPER `mlib_devel` directory, the source notes show:

```bash
source startsg.local
echo "$JASPER_BACKEND"
echo "$XLNX_DT_REPO_PATH"
ls "$XLNX_DT_REPO_PATH"
```

The expected backend value is:

```text
vitis
```

Then launch the toolflow and build:

```bash
./startsg
```

Inside MATLAB, run:

```matlab
jasper
```

The setup document records successful outputs with names of the form:

```text
t1_YYYY_MM_DD.fpg
t1_YYYY_MM_DD.dtbo
```

The exact prefix and date depend on the design and build. Check the actual build output and confirm both files exist rather than relying only on the example names.

## 9. References in the setup notes

- CASPER mailing-list archives: https://www.mail-archive.com/casper@lists.berkeley.edu/
- CASPER toolflow documentation: https://casper-toolflow.readthedocs.io/
- CASPER `mlib_devel`: https://github.com/casper-astro/mlib_devel
- StrathSDR System Generator notes for Ubuntu 20.04 / Vivado 2021: https://strath-sdr.github.io/tools/matlab/sysgen/vivado/linux/2021/01/28/sysgen-on-20-04.html

See also [Version compatibility and scope](01-version-matrix-and-scope.md) before using this procedure with another release.
