---
title: projects/armv6 branch is no more
date: '2012-08-17T19:18:43'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

ARMv6/AMRv7 support is [now in main FreeBSD codebase](http://lists.freebsd.org/pipermail/freebsd-hackers/2012-August/040263.html).

New goodies:

- General ARMv6/ARMv7 kernel bits (pmap, cache,
    assembler routines, etc...)
- ARM SMP support
- VFP/Neon support
- ARM Generic Interrupt Controller driver
- Improved thread-local storage for cpus >=ARMv6
- Two new values for TARGET_ARCH: armv6 and armv6eb
- Driver for SMSC LAN95XX and LAN8710A ethernet controllers
- Marvell MV78x60 support (multiuser, ARMADA XP kernel config)
- TI OMAP4 and AM335x support (multiuser, no GPU or graphics
    support, kernel configs for Pandaboard and Beaglebone)
- LPC32x0 support (multiuser, frame buffer works with SSD1289
    LCD controller.Embedded Artists EA3250 kernel config)
- Barebone Nvidia Tegra2 support (timers, interrupts and UART.
    No kernel config)

I'm going to re-create Raspberry Pi port off HEAD and start merging least intrusive bits back to the tree.
