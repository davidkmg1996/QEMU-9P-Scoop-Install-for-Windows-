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
- testing Windows-host to Linux-guest 9P filesystem access in both directions; and
- packaging and distributing the resulting build.
- indexing scoop bucket for QEMU with Windows 9P support (previously unavailable)

The original QEMU project, WINQ-EMU project, and their respective contributors retain their applicable copyrights and attribution.

## What is different?

This project is based on prior Windows 9P work contributed to QEMU and subsequently developed in the WINQ-EMU project. The Windows 9P implementation itself is **not claimed as original work by this project**.

The main work in this project is integrating that existing Windows 9P implementation into the QEMU 11.1.1 source tree, resolving compatibility and source-integration issues, building it for Windows, validating the resulting system, and creating/indexing a scoop bucket which contains QEMU with Windows 9P support, which was previously unavailable.

This build incorporates subsequent Windows 9P correctness fixes, including improvements to Windows extended-attribute error handling and `openat()` file-descriptor handling. During integration, additional Windows-specific adjustments were made where the Unix directory-file-descriptor model does not apply to Windows.

The Windows build system was also adjusted to avoid the Unix-style symlink installation step, allowing the resulting QEMU binaries and runtime dependencies to be distributed as a portable Windows package.

The resulting build was compiled and tested with a Linux guest using QEMU's `virtio-9p-pci` device and a Windows host directory. Bidirectional file access between the Windows host and Linux guest was successfully verified.

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
* 
## License

QEMU is licensed under the **GNU General Public License version 2 (GPL-2.0)**.

See the QEMU source and license files for complete licensing information.
