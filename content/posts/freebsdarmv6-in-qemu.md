---
title: FreeBSD/armv6 in QEMU
date: '2012-12-05T22:48:55'
draft: false
author: Oleksandr Tymoshenko
categories:
- ARM
---

**[QEMU 1.5 users see [this update](http://kernelnomicon.org/?p=395)]**

First take at getting FreeBSD/armv6 running in simulators. Simulators are great for tracking down nasty bugs and building packages. 

So here is support for Versatile Platform Board machine supported by QEMU. Most likely this code will not run on real VersatilePB because I do not have this hardware and timing code (or lack of it) on CLCD driver and Keyboard/Mouse interface (PL050) is pure guesswork. 

Back to gory details:

### Build

You'll need [this patch](http://people.freebsd.org/~gonzo/arm/qemu/versatilepb.diff) and [this script](http://people.freebsd.org/~gonzo/arm/qemu/build-versatile.sh). Apply patch, use script to get freebsd-versatilepb.flash. 

As for userland - it's fully compatible with Raspberry Pi's userland, or Pandaboard's one. So you can use latest RPi SD card image. As for now it's [freebsd-pi-r243778.img.gz](http://people.freebsd.org/~gonzo/arm/rpi/freebsd-pi-r243778.img.gz) (124Mb)

### Run QEMU

I believe that at least QEMU 1.2.0 is required. It's still 1.1.1 in ports due to some blockers that prevent upgrade to 1.3.0. [This patch](http://people.freebsd.org/~nox/tmp/qemu-devel-1.3.0-partly-004.patch) updates port to 1.3.0 and it worked for me. Also I tested images with QEMU on windows and OS X - works fine.

```
qemu-system-arm -M versatilepb -m 128M -kernel freebsd-versatilepb.flash  -cpu arm1176 -hda freebsd-pi-r243778.img 
```

### Caveats

- Serial console is off by default, use graphics console. If you need headless mode, rebuild image with "device sc" and related options disabled or use prebuilt flash image for headless mode
- root device name is hardcoded so if you're using some other image or building your own - be sure that's ROOTDEV actually match real root
- Memory size is hardcoded - 128M. For getting this information run-time we'll need uboot and ubldr added to boot chain

### Prebuilt kernels

[freebsd-versatilepb.flash](http://people.freebsd.org/~gonzo/arm/qemu/freebsd-versatilepb.flash) (4Mb)
[freebsd-versatilepb-headless.flash](http://people.freebsd.org/~gonzo/arm/qemu/freebsd-versatilepb-headless.flash) (4Mb)
MD5 (freebsd-versatilepb-headless.flash) = 24a41807bf94c5fec0565adcfef48678
MD5 (freebsd-versatilepb.flash) = 085dedae67895ac1d1a7c04c7cda8468
