---
title: State of FreeBSD/MIPS emulation
date: '2013-09-04T15:22:24'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

A week ago Adrian Chadd asked me to take a loot at FreeBSD/MIPS emulation. So last week I've been busy tidying up stuff in that department and looking up bits of information on various emulators. This morning I finally committed last changeset so now is the time to write up summary.

## Emulators

There are two widely used MIPS emulatoes that FreeBSD supports: [QEMU](http://www.qemu.org) and [GXemul](http://gxemul.sourceforge.net). Both of them support numerous MIPS devices but we're interested in only two. 

[MALTA](http://www.mips.com/products/development-kits/malta/) is more or less standard for MIPS emulation and supported by both emulators. QEMU supports 32 and 64 bit variants with both big and little-endian byte order. So four modes in total. Also for MALTA machine QEMU provides PCNet NIC emulation. 

GXemul supports 32 and 64 bit modes of MALTA but only for little-endian byte order. Big-endian byte order is not supported due to incomplete PCI controller implementation. No NIC support for MALTA machine. Also Gxemul provides so-called oldtestmips emulation mode: generic implementation of abstract MIPS machine with very simplified NIC/disk devices. In theory it should be faster then actual hardware emulation but I haven't got around to benchmarking yet. oldtestmips can be run in 32 and 64 bit mode, but only big-endian byte order is supported.

## Disk images

I used raw disk images created by `makefs(8)` utility. The advantage of raw disk image is that they can be used with both emulators. qemu provides more options in this area but they're out of scope of this article. I created four images for my tests: disk.img, disk64.img, diskel.img, and disk64el.img. 32-bit big-endian, 64-bit big-endian, 32-bit little-endian, 64-bit big-endian. The process looks somewhat like this script:

```
#!/bin/sh
set -e

export TARGET=mips
export TARGET_ARCH=mips
export SRCCONF=/dev/null
export SRCROOT=/src/FreeBSD/head
export MAKEOBJDIRPREFIX=/src/FreeBSD/obj/head
export DESTDIR=/src/FreeBSD/tftproot/$TARGET_ARCH
make -C $SRCROOT buildworld

sudo -E mkdir -p /src/FreeBSD/tftproot/$TARGET_ARCH
sudo -E make -C $SRCROOT KERNCONF=$KERNCONF DESTDIR=$DESTDIR installworld
sudo -E make -C $SRCROOT KERNCONF=$KERNCONF DESTDIR=$DESTDIR distribution

# modify /etc/fstab and /etc/rc.conf in $DESTDIR

# Create 512Mb disk image with big-endian UFS
# For TARGET_ARCH set to mipsel or mips64el use "-B le" switch 
sudo -E makefs -M 538968064 -B be /src/FreeBSD/disk.img $DESTDIR
```

Change TARGET_ARCH and disk image name accordingly for mipsel/mip64/mips64el targets.

## QEMU

As of QEMU 1.6.0 there is one known problem with it: NIC emulation does not work in big-endian mode. See [this patch](http://lists.nongnu.org/archive/html/qemu-stable/2013-08/msg00217.html) for fix and details. Also, amount of memory limited to 128Mb. Use `MALTA` kernel config for 32-bit mode and `MALTA64` for 64-bit mode. 

Command lines for various modes/byte orders are:

```
qemu-system-mips -M malta -kernel /path/to/mips.mips/MALTA/.../kernel -hda /src/FreeBSD/disk.img -nographic
qemu-system-mips64 -M malta -kernel /path/to/mips.mips64/.../MALTA64/kernel -hda /src/FreeBSD/disk64.img -nographic
qemu-system-mipsel -M malta -kernel /path/to/mips.mipsel/.../MALTA/kernel -hda /src/FreeBSD/diskel.img -nographic
qemu-system-mips64el -M malta -kernel /path/to/mips.mips64el/.../MALTA64/kernel -hda /src/FreeBSD/disk64el.img -nographic
```

 

## GXemul

GXemul requires two patches for proper FreeBSD/MIPS emulation. First one implements [rdhwr op for reg 29](http://sourceforge.net/p/gxemul/code/5799/) required by TLS support. The second one [fixes UDP packet checksum calculation](http://sourceforge.net/p/gxemul/code/5803/) and required for proper functioning of emulated DHCP server. Both of these patches have been committed upstream but there were no GXemul release after that so they're not available as part of `emulators/gxemul` port. 

As with QEMU kernel configs for MALTA are `MALTA` and `MALTA64`. And since only little-endian byte order is supported command lines for this emulation mode are:

```
gxemul -e malta -C 4Kc -d /home/gonzo/FreeBSD/diskel.img /path/to/mips.mipsel/.../MALTA/kernel
gxemul -e malta -d /home/gonzo/FreeBSD/disk64el.img /path/to/mips.mips64el/.../MALTA64/kernel
```

For oldtestmips emulation mode is other way around, only big-endian. And to make things more complicated it's `GXEMUL` kernel config for 64-bit and `GXEMUL32` for 32-bit. So here you are:

```
gxemul -M 256 -E oldtestmips -d /home/gonzo/FreeBSD/disk64.img /path/to/mips.mips64/.../GXEMUL/kernel
gxemul -M 256 -C 4Kc -E oldtestmips -d /home/gonzo/FreeBSD/disk.img /path/to/mips.mips/.../GXEMUL32/kernel
```

Maximum amount of memory set could be set by `-M` command-line switch for oldtestmips emulation is 256M. Otherwise physical memory would overlap with memory-mapped regions reserved by devices. There are some preliminary work to work around this limitation but no results yet.
