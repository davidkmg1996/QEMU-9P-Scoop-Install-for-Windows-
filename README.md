# QEMU 9P for Windows

An unofficial patched Windows build of QEMU with **virtio-9p host filesystem sharing**.

This project integrates existing Windows 9P support into the QEMU 11.1.1 source tree and provides a portable Windows build that can expose a Windows host directory to a Linux guest through QEMU's `virtio-9p` device.

## Download

**Latest Windows x86-64 release:**
[Download QEMU 9P for Windows](https://github.com/davidkmg1996/QEMU-9P-Scoop-Install-for-Windows-/releases/latest)

The release includes a portable Windows build with `virtio-9p` support. No traditional installation is required.

## Provenance and attribution

The Windows 9P implementation incorporated by this project is derived from earlier Windows 9P work in QEMU and WINQ-EMU.

In particular, this project builds upon the Windows 9P porting work associated with **Bin Meng, Guohuai Shi, and the QEMU/WINQ-EMU contributors**.

This project does **not** claim authorship of the underlying Windows 9P implementation.

The project's original work primarily consists of:

- integrating the existing Windows 9P work into the QEMU 11.1.1 source tree;
- resolving source compatibility and integration issues;
- making the Windows build-system adjustment required for portable packaging;
- compiling and validating the Windows build;
- testing Windows-host to Linux-guest 9P filesystem access in both directions
- packaging and distributing the resulting build; and
- indexing scoop bucket for QEMU with Windows 9P support (previously unavailable)

The original QEMU project, WINQ-EMU project, and their respective contributors retain their applicable copyrights and attribution.

## Changes made by this project

The following work was performed specifically for this project and release:

* Integrated the existing Windows `virtio-9p` implementation and subsequent Windows 9P correctness fixes into the QEMU 11.1 development tree.
* Resolved source compatibility and integration issues encountered while applying the Windows 9P work to this QEMU source tree.
* Added the Windows-specific handling required where Unix directory file-descriptor behavior is not available on Windows.
* Adjusted the Windows Meson build configuration to skip the Unix-style symlink installation step, allowing the build to be packaged as a portable Windows distribution.
* Built the Windows x86-64 `x86_64-softmmu` target with WHPX, SDL, and `virtio-9p` support.
* Validated `virtio-9p` host filesystem sharing between a Windows host and Linux guest.
* Verified bidirectional file access between the Windows host directory and Linux guest.
* Created the portable Windows distribution and packaged the required runtime dependencies.
* Created and published the accompanying Scoop bucket and package manifest for the Windows 9P build.

### Original source-level integration

In addition to integrating the existing Windows 9P work, this project required source-level changes to make that implementation work correctly in the QEMU 11.1 development tree.

These included:

* Resolving the `local_fid_fd()` integration for Windows in `hw/9pfs/9p-local.c`. Windows does not provide the same `dirfd()` behavior for an open `DIR *` as Unix systems, so the Windows implementation returns `ENOTSUP` for directory file-descriptor requests rather than attempting to use the Unix `dirfd()` API.
* Resolving source compatibility issues between the imported Windows 9P implementation and the QEMU 11.1 source tree.
* Integrating the Windows-specific filesystem, directory, error-handling, and 9P utility interfaces required by the port.
* Integrating the Windows/Linux errno translation and Windows-specific filesystem utility code required by the implementation.
* Incorporating and validating subsequent Windows 9P correctness fixes, including the Windows extended-attribute error handling and `openat()` file-descriptor handling.

The original Windows 9P implementation itself is attributed to its respective upstream contributors; the source-level integration and compatibility work described above was performed for this project.

### Build-system modification

This project also includes a Windows-specific Meson build-system modification.

The top-level `meson.build` was changed so that the Unix-style `symlink-install-tree.py` post-configuration step is skipped when building on Windows:

```meson
if host_machine.system() != 'windows'
  meson.add_postconf_script(find_program('scripts/symlink-install-tree.py'))
endif
```

This was necessary to allow the Windows build to be installed into a portable directory without relying on the Unix-style symlink installation mechanism.

### Original build, validation, and packaging work

This project also performed the following original release work:

* Configured a Windows QEMU build with `x86_64-softmmu`, WHPX, SDL, and `virtfs`/9P support.
* Compiled the complete Windows build successfully.
* Verified that the resulting executable exposes the `virtio-9p` devices.
* Tested Windows-host to Linux-guest filesystem sharing using `virtio-9p-pci`.
* Verified bidirectional file creation, reading, and writing between the Windows host and Linux guest.
* Identified and documented that the tested working Linux mount configuration uses `trans=virtio` without explicitly specifying `version=9p2000.L`.
* Assembled the required Windows runtime DLL dependencies into a portable distribution.
* Produced the portable ZIP release and its corresponding SHA-256 checksum.
* Created the Scoop manifest and configured it to distribute the portable build as a separate `qemu-9p` package.


This is **not an official QEMU release**.

## 9P host sharing

Example:

```text
qemu-system-x86_64.exe ^
  -accel whpx ^
  -m 4096 ^
  -smp 4 ^
  -fsdev local,id=fsdev0,path=C:/9p-share,security_model=none ^
  -device virtio-9p-pci,fsdev=fsdev0,mount_tag=hostshare
```

Inside a Linux guest:

```text
sudo mkdir -p /mnt/hostshare
sudo mount -t 9p -o trans=virtio hostshare /mnt/hostshare
```

The Windows host directory can then be accessed from the Linux guest through 9P.

> **Note:** The tested working mount command uses `trans=virtio` without explicitly specifying `version=9p2000.L`.

## Portable deployment

The prebuilt Windows package is designed for portable use and does not require a traditional system-wide installation.

The package includes the QEMU executable and required runtime dependencies in a self-contained directory.

For the x86-64 system emulator, launch:

```text
qemu-system-x86_64.exe
```

You can run it directly from the extracted package directory or add that directory to your `PATH`.

Users should follow the policies and rules of the system or organization on which the software is used.

## Scoop

Prebuilt Windows binaries are distributed through the accompanying Scoop bucket.

The Scoop package installs this patched QEMU build without replacing the official QEMU package.

Install with:

```powershell
scoop bucket add qemu-9p https://github.com/davidkmg1996/QEMU-9P-Scoop-Install-for-Windows-
scoop install qemu-9p/qemu-9p
```
<img width="1008" height="180" alt="Screenshot 2026-09-08 220251" src="https://github.com/user-attachments/assets/3dc376da-de72-4639-84d6-a25edfebc77e" />

## Build information

Based on the QEMU **11.1 development tree**.

This build reports version **11.1.50** and is **not an official QEMU release**.

Target:

* Windows x86-64
* WHPX
* SDL
* virtio-9p
* `x86_64-softmmu`
  
## License

QEMU is licensed under the **GNU General Public License version 2 (GPL-2.0)**.

See the QEMU source and license files for complete licensing information.
