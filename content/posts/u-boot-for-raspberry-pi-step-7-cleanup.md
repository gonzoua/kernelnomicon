---
title: 'U-Boot for Raspberry Pi, step 7: cleanup'
date: '2012-06-17T19:32:26'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

More progress on the subject: 

- I cleaned up sources and pushed new version to github.
- Stephen Warren submitted patches for R-Pi support to U-Boot mailing list [here](http://lists.denx.de/pipermail/u-boot/2012-June/125834.html) and [here](http://lists.denx.de/pipermail/u-boot/2012-June/125835.html). His version is much cleaner so I spent some time combining his patches with ported USB driver. So now target is called rpi_b and you should use "make rpi_b_config" instead of "make raspberry_pi_config" step when building U-Boot from sources.
- Pre-built binaries are available for download [here](http://people.freebsd.org/~gonzo/arm/rpi/rpi-uboot.tar.gz).
