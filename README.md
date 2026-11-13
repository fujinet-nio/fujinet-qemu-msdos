# QEMU MS-DOS for FujiNet

This repository contains QEMU helpers and a base MS-DOS 6.22 hard disk image
for testing `fujinet-nio` and the MS-DOS NIO `FUJINET.SYS` driver.

This repository does not ship an MS-DOS image. Build a bootable MS-DOS 6.22 disk
with [qemu-msdos-init](https://github.com/fujinet-nio/qemu-msdos-init) from your
own MS-DOS install disks, and point `BASE_IMAGE` at it.

## NIO Quick Start

Build the NIO qcow2 image with driver and apps:

```sh
export BASE_IMAGE=/path/to/qemu-msdos-init/build/msdos622-cf-2047m-auto.img
./build-nio-qcow --apps-manifest manifests/apps.yaml
```

Run it:

```sh
./run-qemu-nio --hda build/msdos-nio-apps.qcow2
```

Run it in the terminal with QEMU curses display:

```sh
./run-qemu-nio --hda build/msdos-nio-apps.qcow2 --display curses
```

Run the same image against a real serial FujiNet device instead of POSIX
`fujinet-nio`:

```sh
./run-qemu-nio --transport serial --serial-dev /dev/ttyUSB0 \
  --hda build/msdos-nio-apps.qcow2
```

The detailed NIO guide is in [docs/nio-workflow.md](docs/nio-workflow.md).
It covers prerequisites, building `fujinet-nio`, building `FUJINET.SYS`,
creating `manifests/apps.yaml`, creating raw FAT disk images, and the DOS
`FHOST` / `FIN` / `FMOUNT` workflow. Generated app images place utilities in
`C:\FNAPPS` and add that directory to `PATH`.

## Repository Contents

- `build-nio-qcow`: creates generated NIO qcow2 images under `build/` from
  `BASE_IMAGE`.
- `build-apps-img`: creates raw FAT images from the app manifest for TNFS or
  FujiNet disk mounting.
- `run-qemu-nio`: starts QEMU with FujiBus over TCP serial or a real host
  serial device. It can also use QEMU's curses display for terminal-based UI
  development, and can copy a boot/config disk into `fujinet-data` for POSIX
  `fujinet-nio`.
- `scripts/create_msdos_img.py`: creates raw FAT12/FAT16 images for mounting
  through FujiNet.
- `manifests/apps.example.yaml`: example app injection manifest.
- `fujinet-data/`: host filesystem root exposed to `fujinet-nio`.

Generated files under `build/` are not committed.
