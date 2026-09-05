---
title: Update on FreeBSD/armv6 in QEMU
date: '2013-06-29T17:37:58'
draft: false
author: Oleksandr Tymoshenko
categories:
- ARM
- FreeBSD
---

QEMU support in FreeBSD/armv6 regressed since I tried it last time few months back. Changes in FreeBSD kernel and in QEMU itself revealed bugs that were masked by previous behaviour. 

In FreeBSD it was r248467: the way memory/IO resources are activated on FDT bus has been changed and it triggered bug in versatile_pci.c

The other issue is more complex. It seems that PCI IRQ routing in QEMU was out of sync with real hardware. So after [commit 66a96d7018b9cbabb73c9b87b62a37e4cc46580a](https://github.com/qemu/qemu/commit/66a96d7018b9cbabb73c9b87b62a37e4cc46580a) IRQ numbers assigned to PCI devices by FreeBSD kernel by default were invalid. Authors of QEMU eventually added compatibility knob to fall back to previous logic. So if you're using QEMU 1.5 or later add this option to your command line:

```
-global versatile_pci.broken-irq-mapping=1
```
