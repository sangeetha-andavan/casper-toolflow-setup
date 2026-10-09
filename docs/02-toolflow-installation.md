# 2. Install the CASPER toolflow

This procedure follows the project record for Ubuntu 20.04, MATLAB R2021a, Vivado/Vitis Model Composer 2021.1, Python 3.8, and a ZCU216. Confirm compatibility before following it.

## 2.1 Install system packages

```bash
sudo apt update
sudo apt install -y git build-essential python3.8 python3.8-venv python3.8-dev
```

Optional packages used for board bring-up in the project notes:

```bash
sudo apt install -y ipython minicom net-tools nmap
```

`ipython` can also be installed inside the virtual environment with pip. Avoid mixing system Python packages and the virtual environment unnecessarily.

## 2.2 Clone the matching toolflow branch

```bash
mkdir -p ~/casper-work
cd ~/casper-work
git clone -b m2021a https://github.com/casper-astro/mlib_devel.git
cd mlib_devel
```

Record the exact revision:

```bash
git branch --show-current
git rev-parse HEAD
git status --short
```

Install Python dependencies in a virtual environment:

```bash
python3.8 -m venv ~/casper-work/casper_venv
source ~/casper-work/casper_venv/bin/activate
python -m pip install --upgrade pip setuptools wheel
cd ~/casper-work/mlib_devel
python -m pip install -r requirements.txt
```

Some historical CASPER dependencies may be sensitive to pip versions. If dependency installation fails, preserve the full error and consult the official documentation before pinning older packaging tools.

Initialise submodules if required by the branch/tutorial you are using:

```bash
git submodule init
git submodule update
```

## 2.3 Install vendor tools

The recorded working setup used:

- MATLAB R2021a Update 8
- Simulink
- DSP System Toolbox
- Signal Processing Toolbox
- Fixed-Point Designer
- Vivado ML Enterprise 2021.1
- Vitis HLS where required by the selected platform
- Vitis Model Composer 2021.1

The supplied notes report that additional MATLAB toolboxes were avoided due to a System Generator initialisation/compile problem. This is a project observation, not a universal instruction to uninstall toolboxes. Install the toolboxes required by your model and the supported toolchain. Use the vendor installers and licence arrangements appropriate to your institution.

## 2.4 Configure `startsg.local`

Locate the template in the `mlib_devel` checkout. Make a copy before editing; exact template paths and contents can vary by branch.

Example settings recorded for the 2021.1 installation (adjust all paths to your own installation):

```bash
source /tools/Xilinx/Vivado/2021.1/settings64.sh
source /tools/Xilinx/Vitis/2021.1/settings64.sh
export JASPER_BACKEND=vitis
export XLNX_DT_REPO_PATH="$HOME/sandbox/xilinx/device-tree-xlnx"
```

The Vitis and device-tree variables matter for the documented RFSoC workflow that generates a device-tree overlay (`.dtbo`). Do not add these blindly to unrelated platform builds. Check the matching tutorial for the backend used by your branch.

Verify the environment in the same terminal from which you launch the toolflow:

```bash
source startsg.local
printf 'JASPER_BACKEND=%s\n' "$JASPER_BACKEND"
printf 'XLNX_DT_REPO_PATH=%s\n' "$XLNX_DT_REPO_PATH"
command -v vivado
```

If the device-tree path is configured, confirm that the directory exists and that its checked-out branch matches the vendor release.

## 2.5 Validate the installation

Before building your own design:

1. Confirm MATLAB/Simulink starts successfully.
2. Confirm the expected CASPER libraries initialise without errors.
3. Open and build a known tutorial design for the matching platform.
4. Confirm the expected output files are generated.
5. Save the full build log and record tool versions and Git commit hashes.

The project notes report successful generation of an `.fpg` and subsequently a `.dtbo` for a ZCU216 tutorial platform. Reaching the end of one build is a useful check, but still test the generated image and board workflow.

## Related documentation

- [Version compatibility and scope](01-version-matrix-and-scope.md)
- [ZCU216 bring-up with `casperfpga`](03-casperfpga-and-board-bringup.md)
- [Troubleshooting](04-troubleshooting.md)
- [Official installation reference](https://casper-toolflow.readthedocs.io/en/latest/src/Installing-the-Toolflow.html)
