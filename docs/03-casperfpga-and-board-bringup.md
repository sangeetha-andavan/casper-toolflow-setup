# 3. Install `casperfpga` and bring up the ZCU216

This page records the Python-side connection and board bring-up sequence from the supplied project notes. It is separate from installing the MATLAB/Vivado CASPER build toolflow.

## 3.1 Create a Python environment

Use the Python version that matches the selected CASPER release. The supplied setup used Python 3.8 and the `py38` branch.

```bash
python3.8 -m venv ~/casper-work/casperfpga_venv
source ~/casper-work/casperfpga_venv/bin/activate
python -m pip install --upgrade pip setuptools wheel
```

Install from the documented repository branch:

```bash
git clone -b py38 https://github.com/casper-astro/casperfpga.git
cd casperfpga
python -m pip install .
```

If the install fails, save the full build log and check the upstream installation guide. The exact install method can vary by release.

Check which package is being imported:

```bash
python - <<'PY'
import casperfpga
print(casperfpga.__file__)
PY
```

## 3.2 Prepare the ZCU216 SD card

The source notes describe writing a CASPER-provided ZCU216 image to an SD card. Obtain the image from the appropriate official CASPER resource or lab archive; this repository does not distribute a board image.

**Destructive operation warning:** writing an image erases the selected target. Identify the SD card device carefully and verify its capacity before using `dd`.

```bash
lsblk -o NAME,SIZE,MODEL,FSTYPE,MOUNTPOINTS
```

After identifying the correct whole-device path and unmounting its partitions, a generic example is:

```bash
# EXAMPLE ONLY. Replace /dev/sdX with the verified SD-card device.
# Do not use a partition such as /dev/sdX1 as the image target.
sudo dd if=zcu216_casper.img of=/dev/sdX bs=4M status=progress conv=fsync
sync
```

The source notes mention an archive named `zcu216_casper.img.tar.gz`; archive contents and image naming may vary. Inspect the archive before use and confirm the resulting image is the one intended for your board and toolflow version.

## 3.3 Start the board and inspect serial output

1. Insert the prepared SD card into the board.
2. Connect a compatible USB serial console.
3. Identify the serial device using system tools rather than assuming a fixed name.
4. Open the serial console with settings specified by the matching board image documentation.
5. Power the board and wait for boot messages.

The supplied notes used `minicom`. The specific `/dev/ttyUSB*` device and serial parameters depend on the host adapter and image. Use the official image documentation if the console output is unreadable or boot does not complete.

## 3.4 Configure the Ethernet link

The project notes give an example host address of `169.254.174.100/16` and board address of `169.254.174.200/16`. These are examples from one setup, not universal defaults.

Confirm the current board IP from its console or image configuration. Configure a host address on the same subnet using your system's network manager or the appropriate command for the chosen interface. Avoid changing unrelated network interfaces.

Test link and reachability:

```bash
ip address
ip route
ping <BOARD_IP>
```

Replace `<BOARD_IP>` with the IP shown by your board. If the board is not reachable, check link status, subnet, the selected host interface, and the board's boot messages.

## 3.5 Connect using `casperfpga`

The Python connection pattern recorded in the notes is:

```python
import casperfpga

fpga = casperfpga.CasperFpga("<BOARD_IP>")
print(fpga.is_connected())
```

Replace `<BOARD_IP>` with the board's actual IP address. Run this inside the virtual environment where `casperfpga` was installed. If connection fails, first verify network reachability and that the board has finished booting.

## 3.6 Program firmware and test a register

Use the `.fpg` file built for the exact target platform and design. Keep build products and logs associated with their source revision.

```python
import casperfpga

fpga = casperfpga.CasperFpga("<BOARD_IP>")
if not fpga.is_connected():
    raise RuntimeError("Could not connect to the FPGA")

fpga.upload_to_ram_and_program("path/to/design.fpg")
```

The exact register name and read/write calls depend on the design's generated memory map. Use a known tutorial design with a documented counter or test register, then verify that its value behaves as expected. Do not copy register names from a different bitstream.

## 3.7 Shutdown and recovery

Shut down Linux on the board cleanly using the method supported by the image, then wait for the board to finish before removing power. Abrupt power loss was associated with boot or filesystem problems in the supplied notes.

If the board stops booting after an abrupt power loss:

1. Remove power and the SD card.
2. Identify the card and its partitions with `lsblk`.
3. Back up or image the card first if its contents matter.
4. Check filesystem type and partition layout before choosing a filesystem repair tool.
5. Run repair tools only on the verified SD-card partitions, never on a guessed device path.

See [Troubleshooting](04-troubleshooting.md) for cautious recovery guidance.

## Official references

- [CASPER `casperfpga` installation guide](https://casper-toolflow.readthedocs.io/en/latest/src/How-to-install-casperfpga.html)
- [CASPER `casperfpga` source repository](https://github.com/casper-astro/casperfpga)
- [AMD/Xilinx ZCU216 platform page](https://www.amd.com/en/products/adaptive-socs-and-fpgas/evaluation-boards/zcu216.html)
