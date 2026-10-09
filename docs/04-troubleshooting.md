# 4. Troubleshooting notes

This page separates behaviours reported in the supplied project documents from general troubleshooting suggestions. Commands that change system libraries, shells, partitions, or filesystems can have broad effects; do not apply them without confirming the exact versions and failure mode.

## 4.1 Python packaging error involving `bdist_wheel`

**Reported symptom:** a package build fails with an error mentioning `bdist_wheel`.

Inside the intended virtual environment, first update the build helpers:

```bash
python -m pip install --upgrade pip setuptools wheel
```

Then repeat the documented dependency-installation command and preserve the complete output if it still fails. Older dependencies can have Python- or pip-version constraints; use the versions supported by the chosen CASPER branch rather than pinning packages at random.

## 4.2 Vivado installer appears stuck

**Observed in project notes:** the Vivado 2021.1 installer reportedly appeared stalled during installation.

Before restarting or deleting files, confirm whether the installer is still active, check disk space, inspect installer logs, and verify that the installer was obtained intact. Avoid running concurrent installer copies or killing a process while it is writing files. Follow the vendor's instructions for the specific installer.

The supplied notes also mention running installers with elevated privileges, but do not generalise that to all Xilinx installers. Use the procedure specified for the exact package and installation method.

## 4.3 MATLAB/Simulink error involving `libhogweed.so.5` / `__gmpn_cnd_sub_n`

**Reported error:**

```text
Error loading ... mwlibmwsimulink_builtinimpl.so
/lib/x86_64-linux-gnu/libhogweed.so.5:
undefined symbol: __gmpn_cnd_sub_n
```

**Cause recorded in project notes:** a Xilinx-shipped `libgmp.so` was shadowing the system library and causing a symbol mismatch.

The source notes propose moving vendor `libgmp.so*` files into an `exclude` directory in Vivado and Model Composer library directories. This is a potentially disruptive, installation-specific change and can break vendor software or be undone by an update.

**Safer triage first:**
- Preserve the full error and the MATLAB/Vivado/Model Composer versions.
- Inspect `LD_LIBRARY_PATH` and the actual libraries loaded by the process.
- Test in a clean shell without custom library-path overrides.
- Back up any vendor installation before changing files.
- Consult the [CASPER toolflow documentation](https://casper-toolflow.readthedocs.io/en/latest/) and vendor support resources.

Only consider changing bundled libraries if the conflict is confirmed for the exact installation and you have a backup and rollback plan. Do not blindly move every `libgmp.so*` file.

## 4.4 System Generator hangs during initialisation or compilation

**Observed in project notes:** the author associated this behaviour with some MATLAB toolboxes and reported a minimal installed set: MATLAB, Simulink, DSP System Toolbox, Signal Processing Toolbox, and Fixed-Point Designer.

A related discussion is available in [AMD Adaptive Support: Model Composer 2021.2 / MATLAB R2021a initialization hang on Ubuntu 20.04.1](https://adaptivesupport.amd.com/s/question/0D52E00006vF6FOSA0/model-composer-v20212-matlab-r2021a-gets-stuck-at-initialization-stage-on-ubuntu-20041?language=en_US). That thread concerns Model Composer 2021.2, so use it as related context rather than assuming it exactly matches the 2021.1 setup described here. This is not proof that MATLAB Compiler or MATLAB Compiler SDK universally causes the problem. Verify the toolboxes actually required by your design and follow the supported CASPER/Xilinx software combination. Avoid removing toolboxes based only on a similar-looking symptom.

## 4.5 Error: FPGA part not found, e.g. `xczu49dr-2-e-es1`

**Observed in project notes:** the platform YAML selected an engineering-sample part while the installed Vivado device catalogue matched production silicon.

Checklist:

1. Identify the actual board/silicon variant.
2. Confirm the matching device family and part are installed in Vivado.
3. Inspect `mlib_devel/jasper_library/platforms/zcu216.yaml`.
4. Compare the platform file with the matching upstream branch.
5. Only adjust the part string if the actual hardware and installed device definitions support that choice.

The setup notes describe changing `xczu49dr-ffvf1760-2-e-es1` to `xczu49dr-ffvf1760-2-e` for production silicon. This is a case-specific change, not a universal fix. See the [CASPER mailing-list discussion 1](https://www.mail-archive.com/casper@lists.berkeley.edu/msg08900.html) and [discussion 2](https://www.mail-archive.com/casper@lists.berkeley.edu/msg09150.html) linked from the setup PDF.

## 4.6 `.fpg` generated but `.dtbo` missing; `XLNX_DT_REPO_PATH` not set

**Observed in project notes:** Jasper generated an `.fpg` but failed to generate a `.dtbo`, reporting that `XLNX_DT_REPO_PATH` was not set.

For the recorded RFSoC/Vitis workflow, check the following in `startsg.local`:

```bash
source /tools/Xilinx/Vivado/2021.1/settings64.sh
source /tools/Xilinx/Vitis/2021.1/settings64.sh
export JASPER_BACKEND=vitis
export XLNX_DT_REPO_PATH="$HOME/sandbox/xilinx/device-tree-xlnx"
```

Replace example paths with the real installation and repository locations. Then verify:

```bash
source startsg.local
echo "$JASPER_BACKEND"
echo "$XLNX_DT_REPO_PATH"
test -d "$XLNX_DT_REPO_PATH" && echo "device-tree repo found"
```

Also verify:
- Vivado and Vitis versions match the selected workflow.
- The device-tree repository branch matches that release.
- `startsg.local` is sourced by the same shell that launches `startsg`.
- The required environment variables are exported and are not only set in another terminal.

See the [official RFSoC tutorial](https://casper-toolflow.readthedocs.io/projects/tutorials/en/latest/tutorials/rfsoc/tut_getting_started.html) and the [CASPER mailing-list discussion](https://www.mail-archive.com/casper@lists.berkeley.edu/msg09031.html).

## 4.7 QT4 / `libcanberra-gtk-module` messages

The project notes report a MATLAB launch message about `canberra-gtk-module` and list GTK package/symlink commands. A missing optional GTK module message may be cosmetic; determine whether MATLAB actually fails before changing system packages.

The notes also mention a legacy Qt4 PPA. Do not add an old PPA or install obsolete Qt4 packages on a modern system without confirming that the exact dependency is required and supported. Prefer the official installer and OS-compatible dependencies.

## 4.8 `dash` versus `bash`

The source notes suggest running:

```bash
sudo dpkg-reconfigure dash
```

and selecting “No” to use bash as `/bin/sh`. This changes system-wide shell behaviour and should **not** be a routine first-line fix. Only consider it if the exact supported toolflow documentation or a confirmed error requires it, and understand the impact on system scripts.

## 4.9 ZCU216 connection fails

If `fpga.is_connected()` is false:

1. Confirm the board has completed booting.
2. Check the serial console for a valid IP address.
3. Verify host and board are on the same subnet and the correct host interface is used.
4. Check Ethernet link status and `ping` the board.
5. Confirm the active venv imports the intended `casperfpga`.
6. Check whether the board image exposes the expected communication service.

Do not assume a particular IP address or `/dev/ttyUSB*` number is universal.

## 4.10 Board hangs after sudden power loss

**Observed in the supplied bring-up notes:** after an abrupt power loss, the board could hang at boot. The notes suspected an unclean FAT boot partition and possible EXT4 root filesystem damage.

Before attempting repair:

1. Power down the board and remove the SD card.
2. Identify the card using `lsblk -o NAME,SIZE,MODEL,FSTYPE,MOUNTPOINTS`.
3. Unmount its partitions.
4. Double-check that the device paths correspond to the SD card, not the host disk.
5. Run filesystem checks on the correct partitions only.

Example from the source notes, **only after verifying the device and partition numbers**:

```bash
sudo umount /dev/sdX1
sudo umount /dev/sdX2
sudo fsck -f /dev/sdX1   # boot partition; check filesystem type first
sudo fsck -f /dev/sdX2   # root partition; check filesystem type first
```

`fsck` may modify the filesystem. Back up or image the card first if data matters. Never use `/dev/sdX` literally; replace it with the verified device. The source notes used `fsck.fat` for the FAT boot partition and `e2fsck` for EXT4 rootfs. Follow the prompts only after confirming the correct partitions.

**Prevention:** shut down Linux cleanly and wait for power-down before switching off the board. Avoid power interruption during filesystem writes or overlay loading.
