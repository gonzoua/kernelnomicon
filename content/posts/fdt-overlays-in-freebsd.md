---
title: FDT overlays in FreeBSD
date: '2015-07-24T15:17:35'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

FDT overlay is an extension to FDT format that lets user to modify base FDT run-time: add new nodes, add new properties to existing nodes or modify existing properties. It's useful when you have base board and some extension units like cape/shield for Pi/BBB or loadable FPGA logic for Zynq. I will not go into details you can find internals described on [Adafruit](https://learn.adafruit.com/introduction-to-the-beaglebone-black-device-tree/device-tree-overlays) or [Raspberry Pi](https://www.raspberrypi.org/documentation/configuration/device-tree.md) websites.

When dealing with overlays there are two options where to handle them: loader or kernel. Managing overlays at kernel level gives more flexibility but requires more related logic, e.g. re-init pinmux after applying overlay, re-run newbus probe/attach. On the other hand loader-level support is quite straightforward and involves nothing but DTB modifications and it's a natural first step to adding FDT overlays to FreeBSD.

Proposed solution is to add fdt_overlays variable that contains coma-separated list of dtbo files, e.g.: "bbb-no-hdmi.dtbo,bbb-4dcape-43.dtbo". This variable can be defined either as a loader(8) variable or as a u-boot env variable. During the boot ubldr load base DTB and right before passing control to the kernel it would go through files, load them from /boot/dtb/ direсtory on root partition and apply to the base blob. Final DTB would be passed to kernel. 

You can find patch and review comments to it on Differential site: [D3180](https://reviews.freebsd.org/D3180). It contains:
- Extension to dtc to generate dynamic symbols and fixup info. 
- ubldr fdt_overlays support 

As Warner Losh mentioned it's not clear yet how to deal with dynamic symbols support patch. It's not part of official dtc tree though it's accepted by RPi and BBB communities.
