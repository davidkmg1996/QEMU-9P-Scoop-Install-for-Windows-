# QEMU 9P for Windows

An unofficial patched Windows build of QEMU with **virtio-9p host filesystem sharing**.

This project is intended to provide a convenient Windows QEMU build that can expose a Windows host directory to a Linux guest through QEMU's `virtio-9p` device.

## What is different?

This build contains patches that add and adapt QEMU's 9P filesystem support for Windows.

The Windows 9P implementation is based on earlier Windows 9P work in QEMU/WINQ-EMU and has been integrated into the QEMU 11.1.50 source tree.

This build also incorporates subsequent Windows 9P correctness fixes, including improvements to Windows extended-attribute error handling and `openat()` file-descriptor handling. During integration, additional Windows-specific adjustments were made where the Unix directory-file-descriptor model does not apply to Windows.

The Windows build system was also adjusted to avoid the Unix-style symlink installation step, allowing the resulting QEMU binaries and dependencies to be distributed as a portable Windows package.

The resulting build was compiled and tested with a Linux guest using QEMU's `virtio-9p-pci` device and a Windows host directory. Bidirectional file access between the Windows host and Linux guest was successfully verified.

This is **not an official QEMU release**.

## 9P host sharing

Example:

```text id="wu0q7i"
qemu-system-x86_64.exe ^
  -accel whpx ^
  -m 4096 ^
  -smp 4 ^
  -fsdev local,id=fsdev0,path=C:/9p-share,security_model=none ^
  -device virtio-9p-pci,fsdev=fsdev0,mount_tag=hostshare
```

Inside a Linux guest:

```text id="1ik7z5"
sudo mkdir -p /mnt/hostshare
sudo mount -t 9p -o trans=virtio hostshare /mnt/hostshare
```

The Windows host directory can then be accessed from the Linux guest through 9P.

## Portable deployment

The prebuilt Windows package is designed for portable use and does not require a traditional system-wide installation.

This can be useful on Windows systems where users do not have administrator privileges or where installing additional software is inconvenient. The package includes the QEMU executable and required runtime dependencies in a self-contained directory.

Users should follow the policies and rules of the system or organization on which the software is used.

## Scoop

Prebuilt Windows binaries are distributed through the accompanying Scoop bucket.

The Scoop package installs this patched QEMU build without replacing the official QEMU package.

<img width="1008" height="180" alt="Screenshot 2026-09-08 220251" src="https://github.com/user-attachments/assets/0eb352b3-1ef5-4001-a500-3b649261b8c1" />


## Build information

Based on QEMU development version **11.1.50**.

Target:

- Windows x86-64
- WHPX
- SDL
- virtio-9p
- `x86_64-softmmu`

## Provenance

The Windows 9P implementation incorporates prior QEMU/WINQ-EMU Windows 9P work. The repository retains the modified source and identifies this project as an unofficial patched build.

The original QEMU project and its contributors retain their respective copyrights.

## License

QEMU is licensed under the **GNU General Public License version 2 (GPL-2.0)**.

See the QEMU source and license files for complete licensing information.
