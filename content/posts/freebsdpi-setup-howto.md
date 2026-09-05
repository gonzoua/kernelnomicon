---
title: FreeBSD/Pi setup HowTo
date: '2012-08-05T19:54:16'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

### This instruction is no longer correct. New version is [here](http://kernelnomicon.org/?p=275)

Took some time but it seems we're there too :) 

Alexander Rybalko, who works on USB drivers for the platform got remote telnet shell to the device: [http://pastebin.com/6NqQLWD2](http://pastebin.com/6NqQLWD2) 

Having fixed serial console for userland applications (two lines change) and after some tweaking of SDHCI driver I got access to multiuser shell too: [multiuser.txt](http://people.freebsd.org/~gonzo/arm/rpi/multiuser.txt)
Still not usable for putting together distros but stable enough for those who are willing to get their hands dirty with kernel side. 

Here is short how to on getting FreeBSD boot on Raspberry Pi:

Get sources:
`
git clone git://github.com/gonzoua/freebsd-pi.git
cd freebsd-pi
git checkout rpi
`
Fetch u-boot files  from here: [freebsd-pi-uboot-20120806-sd.tar.gz](http://people.freebsd.org/~gonzo/arm/rpi/freebsd-pi-uboot-20120806-sd.tar.gz).

Build sources and create SD card image with script like this:
`#!/bin/sh
set -e

export TARGET_ARCH=arm
export SRCROOT=/usr/home/gonzo/Sources/freebsd-pi
export MAKEOBJDIRPREFIX=/usr/home/gonzo/Sources/obj
export KERNCONF=RPI-B
KERNEL_BIN=\`realpath $MAKEOBJDIRPREFIX\`/arm.arm/\`realpath $SRCROOT\`/sys/$KERNCONF/kernel.bin

make -C $SRCROOT kernel-toolchain
make -C $SRCROOT KERNCONF=$KERNCONF WITH_FDT=yes buildkernel
make -C $SRCROOT TARGET_CPUTYPE=armv6 MALLOC_PRODUCTION=yes buildworld

IMG=bsd-pi.img
rm -f $IMG
dd if=/dev/zero of=$IMG bs=128M count=8
MDFILE=\`mdconfig -a -f bsd-pi.img\`
gpart create -s MBR ${MDFILE}
gpart add -s 32m -t '!12' ${MDFILE}
gpart add -s 896m -t '!12' ${MDFILE}
gpart set -a active -i 1 ${MDFILE}
newfs_msdos -L boot -F 16 /dev/${MDFILE}s1
newfs /dev/${MDFILE}s2
mount_msdosfs /dev/${MDFILE}s1 /mnt
tar -x -v -z -C /mnt -f freebsd-uboot-sd.tar.gz
cp $KERNEL_BIN /mnt
umount /mnt
mount /dev/${MDFILE}s2 /mnt
make -C $SRCROOT DESTDIR=/mnt installworld
make -C $SRCROOT DESTDIR=/mnt distribution

# Minimal config 
echo 'hostname="freebsd-pi"' > /mnt/etc/rc.conf
echo '/dev/mmcsd0s2    /               ufs     rw      1       1' > /mnt/etc/fstab

umount /mnt
mdconfig -d -u $MDFILE
`

dd image to SD card, insert it to Raspberry Pi and check serial port for some output

There is also U-Boot configuration that uses dhcp/tftp method by default freebsd-pi-uboot-20120806-netboot.tar.gz. It's more suitable for kernel hacking but requires environment setup.
