---
title: FreeBSD on Jetson TK1
date: '2016-06-28T13:33:41'
draft: false
author: Oleksandr Tymoshenko
tags:
- freebsd
- jetson tk1
- nvidia
categories:
- FreeBSD
---

I finally got around to BSDify my Jetson TK1. Here is short summary of what is involved. And to save you some scrolling here are artifacts obtained from whole ordeal: [https://people.freebsd.org/~gonzo/arm/jetson-tk1/](https://people.freebsd.org/~gonzo/arm/jetson-tk1/)

[![Jetson TK1](/uploads/2016/06/IMG_2064-e1467143789207-768x1024.jpg)](/uploads/2016/06/IMG_2064-e1467143789207.jpg)

## U-Boot

First of all - my TK1 didn't have U-Boot. Type of bootloader depends on the version of Linux4Tegra TK1 comes with. Mine had L4T R19, with some kind of "not u-boot" bootloader. My first attempt was to use tegrarcm tool, it uses libusb, so it's possible to build it on FreeBSD with some elbow grease, but once I tried to run it - it gave me cryptic errors and USB is not my strong skill so I took low road and installed Ubuntu VM. For what is's worth I got the same kind of error on Ubuntu.

Next step was to use official update procedure described in [http://developer.download.nvidia.com/embedded/L4T/r21_Release_v4.0/l4t_quick_start_guide.txt](http://developer.download.nvidia.com/embedded/L4T/r21_Release_v4.0/l4t_quick_start_guide.txt). Since I wasn't going to boot Linux on the board I didn't need sample rootfs. So the whole procedure was:

- Go to L4T [R21.4 page](https://developer.nvidia.com/linux-tegra-r214)
- Download [Tegra124_Linux_R21.4.0_armhf.tbz2](http://developer.download.nvidia.com/embedded/L4T/r21_Release_v4.0/Tegra124_Linux_R21.4.0_armhf.tbz2)
- Unpack it
- Connect microUSB port on device to Linux VM 
- Get device into recover mode: power cycle, press and hold recovery button, press and release power button, release recovery button
- Run `./flash.sh jetson-tk1 mmcblk0p1`, this should rewrite eMMC flash on the board and after reboot you will get u-boot prompt on serial console

## FreeBSD

At this point you can boot FreeBSD on TK1. I use netboot for most of my device so in this case it was: build and deploy world to /src/FreeBSD/tftproot/tk1, build and install kernel to the same directory, copy /src/FreeBSD/tftproot/tk1/boot/kernel/kernel to kernel.TK1 in tftproot directory, add entry do DHCP  config and restart DHCP server. Entry looks like this:

```
host tk1 {
        hardware ethernet 00:04:4b:49:08:9e;
        fixed-address 192.168.10.98;
        filename "kernel.TK1";
        option root-path "/src/FreeBSD/tftproot/tk1";
        option root-opts "nolockd";
        option routers 192.168.10.1;
}
```

And also you need to add this to sys/arm/conf/JETSON-TK1 before building kernel:

```
options        BOOTP
options        BOOTP_NFSROOT
options        BOOTP_COMPAT
options        BOOTP_NFSV3
```

On the device you just run "dhcp; bootelf" and voila - it just works. 

## ubldr

Next step was to get ubldr running. I prefer suing ubldr because it gives more control over boot process accessible from booted FreeBSD system. ubldr requires U-Boot with API support, so I had to rebuild U-Boot from [sources provided by nvidia](http://developer.download.nvidia.com/embedded/L4T/r21_Release_v4.0/source/u-boot_src.tbz2) with added `#define CONFIG_API` and all standard patches from sysutils/u-boot-* ports. Build procedure is standard:

```
export ARCH=arm
export CROSS_COMPILE=arm-linux-gnueabihf-
make jetson-tk1_config
make
```

It will generate multiple files, u-boot-dtb-tegra.bin is the one you want.

To reflash board with non-standard u-boot run `./flash.sh -L /path/to/u-boot-dtb-tegra.bin jetson-tk1 mmcblk0p1`

Back to ubldr. It was easy to build and load it. Build script:

```
#!/bin/sh
export TARGET=arm
export TARGET_ARCH=armv6
export SRCROOT=/src/FreeBSD/wip
export MAKEOBJDIRPREFIX=/src/FreeBSD/obj
export MAKESYSPATH=$SRCROOT/share/mk

set -x
set -e

buildenv=`make -C $SRCROOT TARGET_ARCH=armv6 buildenvvars`
eval $buildenv make -C $SRCROOT/sys/boot -m $MAKESYSPATH obj
eval $buildenv make -C $SRCROOT/sys/boot -m $MAKESYSPATH clean
eval $buildenv make -C $SRCROOT/sys/boot -m $MAKESYSPATH UBLDR_LOADADDR=0x80600000 all

sudo cp /src/FreeBSD/obj/arm.armv6/src/FreeBSD/wip/sys/boot/arm/uboot/ubldr /src/FreeBSD/tftpboot/ubldr.TK1
```

Obviously, kernel.TK1 in DHCP config needs to be replaced with ubldr.TK1. 0x80600000 is some value I came up with by looking at u-boot default environment. Something not high enough to overlap with kernel and not low enough to overlap with u-boot. 

And that's where thing got hairy. To load ubldr and then netboot kernel, you need to set u-boot env loaderdev variable first: `setenv loaderdev net; saveenv`. And then do the same thing as above: `dhcp; bootelf`. Unfortunately I got this:

```
## Starting application at 0x81000098 ...
Consoles: U-Boot console
Compatible U-Boot API signature found @0xffa3e410

FreeBSD/armv6 U-Boot loader, Revision 1.2
(gonzo@eb3.bluezbox.com, Mon Jun 27 19:59:22 PDT 2016)

DRAM: 2048MB
MMC: no card present
MMC Device 2 not found
MMC Device 3 not found
MMC: no card present
MMC: no card present
MMC: no card present
MMC: no card present
MMC: no card present
MMC: no card present
MMC Device 2 not found
Number of U-Boot devices: 3
U-Boot env: loaderdev='net'
Found U-Boot device: disk
Found U-Boot device: net
Booting from net0:
panic: arp: no response for 192.168.10.1

--> Press a key on the console to reboot <--
Rebooting...
resetting ...
```

After some heavy thinking and code digging problem was narrowed down to u-boot network driver drivers/net/rtl8169.c. Instead of returning 0 on success and negative value on error it returns number of bytes sent on success and zero on error. Which confused ubldr into thinking nothing is sent, so recv part of exchange was never invoked. After fixing this issue kernel was loaded just fine but hang right afert

```
Using DTB compiled into kernel.
Kernel entry at 0x0x80800100...
Kernel args: (null)
```

Logn story short - it was caused by enabled D-Cache so I had to add 

```
#ifndef CONFIG_SPL_BUILD
#define CONFIG_SYS_DCACHE_OFF
#define CONFIG_CMD_CACHE
#endif
```

to u-boot config and go through rebuild/reflash cycle again. After this whole boot chain went through right to login prompt.

My next goal is to make TK1 self-contained box: get base system installed on eMMC and use attached SSD as scratch disk for swap and builds.
