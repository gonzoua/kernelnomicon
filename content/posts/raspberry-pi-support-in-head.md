---
title: Raspberry Pi support in HEAD
date: '2016-10-14T22:29:03'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Raspberry Pi 3 limited support was committed to HEAD. Most of drivers should work with upstream dtb, RNG requires attention because callout mode seems to be broken and there is no IRQ in upstream device tree file. SMP is work in progress. There are some compatibility issue with VCHIQ driver due to some assumptions that are true only for ARM platform. 

SD card layout is the same as for RPi and RPi2 but boot chain is different. All ARM64 supported by FreeBSD up to now used EFI as boot environment. RPi 3 has only VC firmware and whatever it can spin off, e.g. u-boot. So it seemed easier to enable EFI API in U-Boot instead of porting ubldr to arm64. There were some hiccups with netbooting, (see [patch](https://people.freebsd.org/~gonzo/arm/patches/u-boot-rpi3-uefi-netboot.diff)) but otherwise it was OK. U-Boot port and crochet config for Pi 3 should be committed "real soon"(tm).

For those who would like to try it ASAP Shawn Webb put together [instruction](https://wiki.freebsd.org/arm64) on how to get bootable SD image.
