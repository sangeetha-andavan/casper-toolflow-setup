# 6. Reproducibility record and validation checklist

This document tracks the setup as reported by the project owner and separates confirmed configuration details from items still needing evidence or confirmation.

## 6.1 Reported environment

| Item | Reported value | Evidence status |
|---|---|---|
| Host OS | Ubuntu 20.04 | Owner-confirmed; capture `lsb_release -a` output for the archive |
| MATLAB | R2021a Update 8 | Owner-confirmed; capture MATLAB `version` output if available |
| Vivado | 2021.1 | Owner-confirmed; capture `vivado -version` output |
| Vitis Model Composer / Vitis | 2021.1 | Owner-confirmed; record exact installed product versions |
| Python | 3.8.10 | Owner-confirmed; capture `python --version` from the intended virtual environment |
| Jasper backend | Vitis | Owner-confirmed; preserve the relevant `startsg.local` lines with local paths redacted |
| FPGA board | ZCU216 | Board family reported; exact silicon variant is **not yet confirmed** |
| Build outputs | `.fpg` and `.dtbo` generated | Owner reports successful generation; attach/review logs and filenames before marking evidence archived |
| Board validation | Board programmed/tested | Owner reports completion; collect the exact test, output, and firmware identity before calling the test fully reproducible |

This table is not a claim that the logs have already been inspected. The owner reports having terminal history, build logs, and generated files available for review.

## 6.2 Capture exact software and source revisions

Run these commands from each existing Git checkout (`mlib_devel`, `casperfpga`, and the device-tree repository, if present):

```bash
git remote -v
git branch --show-current
git rev-parse HEAD
git status --short
```

Save the output in a local text file. Before sharing it, remove private filesystem paths or internal repository URLs if necessary. The commit SHA matters: a branch name such as `m2021a` can point to different commits over time.

Capture host and Python details:

```bash
lsb_release -a
uname -a
python --version
python -m pip freeze
vivado -version
```

Run Python and pip commands only after activating the virtual environment used for CASPER. If a command is not on `PATH`, record that rather than changing your environment just to make the command work.

In MATLAB, run:

```matlab
version
ver
```

Save the output locally. It can include installed product information; review and redact anything private before publishing.

## 6.3 Preserve build evidence

For each known-good build, archive or record:

- The design/tutorial name and the platform YAML file used.
- The relevant `mlib_devel` commit SHA.
- The Vivado/Vitis and MATLAB versions.
- The complete build log, or at minimum the full start and final sections plus all warnings/errors.
- The generated `.fpg` and `.dtbo` filenames and file sizes.
- The exact command or GUI workflow used to launch the build.
- Whether the build exited successfully and how success was determined.

Do **not** commit large binaries, licensed vendor software, board credentials, or sensitive lab files to this public repository. Prefer a short, redacted log excerpt or a checksum manifest. For files kept locally, checksums can be generated with:

```bash
sha256sum path/to/design.fpg path/to/design.dtbo
```

Record the real paths locally; replace the example paths above.

## 6.4 Board variant must be identified

The current record confirms the board family as ZCU216, but the exact production-versus-engineering-sample silicon is unknown. Do not change the platform part string until the physical board and installed Vivado device definition are confirmed.

Record:
- Board label / hardware revision (do not publish serial numbers).
- Whether the board is production silicon or engineering sample, if this can be verified.
- The FPGA part string reported by the platform YAML.
- The part supported by the installed Vivado device catalogue.
- The platform YAML filename and its Git revision.

If the variant cannot be established from available documentation, leave it marked **unknown** and explain the limitation.

## 6.5 End-to-end validation matrix

Use this matrix to turn the reported successful workflow into a repeatable test. Mark each item only after the associated evidence has been reviewed.

| Stage | Test | Evidence to keep | Status |
|---|---|---|---|
| 1 | Correct tool versions and source revisions recorded | Version output and Git SHAs | Pending evidence capture |
| 2 | CASPER libraries initialise in MATLAB/Simulink | Startup log or screenshot with sensitive details removed | Needs evidence |
| 3 | Known tutorial/platform build completes | Build log and design/platform identifiers | Owner reports build success; evidence to review |
| 4 | Expected `.fpg` generated | Filename, size, SHA-256 | Owner reports generated; evidence to review |
| 5 | Expected `.dtbo` generated | Filename, size, SHA-256 | Owner reports generated; evidence to review |
| 6 | ZCU216 boots the intended image | Serial-console excerpt and image identifier | Owner reports board testing; exact evidence to review |
| 7 | Host reaches board over Ethernet | Sanitised connection/ping output | Needs evidence |
| 8 | `casperfpga` connects to the board | Minimal Python test output | Needs evidence |
| 9 | Intended `.fpg` programs the FPGA | Programming log and firmware/design identity | Owner reports board testing; exact evidence to review |
| 10 | Known register/counter test behaves as expected | Register name, test method, expected and observed result | Needs exact test details |
| 11 | Clean shutdown/restart succeeds | Brief test note | Needs evidence |

“Needs evidence” does not mean the test failed; it means this repository does not yet contain enough reviewed evidence to reproduce it independently.

## 6.6 Safe evidence-sharing checklist

Before uploading any log, command history, or configuration:

- [ ] Remove passwords, tokens, private keys, licence files, and credentials.
- [ ] Redact private IP addresses if they reveal lab network details; public example IPs may be documented separately.
- [ ] Replace personal home-directory paths with placeholders where appropriate.
- [ ] Do not upload the proprietary MATLAB/Vivado installers or licensed files.
- [ ] Do not upload full bitstreams or device-tree overlays unless their distribution is permitted and repository size/licensing has been considered.
- [ ] Keep a note of what was redacted so the procedure remains understandable.

## 6.7 Recommended next review

Start with the build log and a redacted terminal history for the successful `.fpg` / `.dtbo` generation. Then review the board programming/register test output. Once those are inspected, update the matrix with evidence-based statuses and the exact board variant if known.
