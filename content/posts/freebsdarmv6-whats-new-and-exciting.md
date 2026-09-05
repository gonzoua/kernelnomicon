---
title: 'FreeBSD/armv6: what''s new and exciting?'
date: '2012-12-30T17:51:03'
draft: false
author: Oleksandr Tymoshenko
categories:
- ARM
- FreeBSD
---

It's been a while since last update on the project status so it might seem as there was no progress in this area. The reality is: there is a bunch of activities happening with various levels of success. So I decided to give kind of end-of-the-year round-up of ongoing projects, plans and obstacles ARM hackers face.

First of all we tried switching default cache type from write-through to write-back type. It should have increased performance but instead opened a can of worms. Memory corruption debugging led to L2 cache driver on Pandaboard, EHCI driver code and subsequently to busdma code. Whole process took quite a few days full of hair-pulling and nagging various people and ended up in committing USB fixes and Ian Lepore's busdma patches. PL310 (L2 cache controller) driver is being tested at this very moment. Original issue (WB caches) still stands and postponed till next year.

Then there are two projects by Andrew Turner aimed at modernizing FreeBSD/armv6 subsystem: [switching to EABI](http://lists.freebsd.org/pipermail/freebsd-arm/2012-December/004311.html) and [clang support for ARM](http://lists.freebsd.org/pipermail/freebsd-arm/2012-December/004390.html). Daisuke Aoyama took both of them and [produced](http://lists.freebsd.org/pipermail/freebsd-arm/2012-December/004331.html) working image for Raspberry Pi. He also fixed two issues with event timers on Raspberry Pi so now the platform is much more stable. I ran buildkernel in a loop overnight and by the morning Pi had survived 7 cycles and still was alive and kicking. I also managed to get python built and working on it. Didn't have 100% success with perl 5.14/5.16, ports were built but failed at install stage segfaulting in do_clean_objs function.

My Pandaboard survived overnight buildkernel loop with L2 cache disabled, but acting up if I enable it. Investigating.

Then there are also several platform bring-ups in progress. Alexander Rybalko works on [getting FreeBSD running on Efika MX Smartbook](http://raybsd.blogspot.com/2012/12/your-first-run-of-freebsd-on-genesi.html). Ganbold Tsagaankhuu hacks on [Allwinner 10](http://lists.freebsd.org/pipermail/freebsd-arm/2012-December/004446.html). Alexander Dutkowski's hardware of choice is [BeagleBoard-xM](http://wiki.freebsd.org/SummerOfCode2012/FreeBSDonBeagleBoardxM).
Ruslan Bukin experiments with Exynos4412 and Thomas Skibo [reported](http://lists.freebsd.org/pipermail/freebsd-arm/2012-December/004469.html) about FreeBSD running on Zedboard (Xilinx Zynq-7000).

But what about devices/platform we have in tree? I have limited knowledge about some platforms so here is summary of the ones I'm aware of. If you have more information on any of these targets (or any other ARM-related projects) - let me know, I'll update post.

- **BCM2835** Raspberry Pi the most accessible and therefore the one that gets the most exposure and testing. Pretty stable, considering. Supported devices: USB, network, MMC, GPIO, framebuffer, GPU. The rest is on ToDo list. VCHIQ driver is BSD-licensed now and I'm planning on getting it to sys/contrib. Userland bits of OpenGL ES should be added as a port though.
- (update) **LPC32x0** No first hand experience but judging by the code it supports MMC, FB, GPIO and USB
- **Marvel Armada XP** I don't have information about this one, sorry
- **Nvidia Tegra2** Just barebone boot stuff.
- **TI AM335x** Examples: BeagleBone, TI Sitara EVM. Network was reported working but unstable on BeagleBone. USB is not supported. Haven't tested GPIO yet.
- **TI OMAP3** Example: BeagleBoard-xM. See Alexander Dutkowski's [project](http://wiki.freebsd.org/SummerOfCode2012/FreeBSDonBeagleBoardxM)
- **TI OMAP4** The hw I have - Pandaboard ES. Supported devices: USB, network, MMC, GPIO. Some issues with L2 cache
- **Versatile Platform Board** Exists only as emulation target for QEMU. Supported hardware: PCI, network, framebuffer. Seems to be fairly stable, no extensive testing performed.

BeagleBone, PandaBoard ad Raspberry Pi images can be built using [Tim Kientzle's scripts](https://github.com/kientzle/freebsd-beaglebone).

Not really stellar list of supported peripherals I'd say. I tend to blame several things.

First - experimental and unstable state of FreeBSD/armv6 in general. It's no fun adding new hardware support when you're not confident in underlying subsystems stability. "I flush cache for this TX descriptor but is it *really* gets flushed?".  Been there, no fun at all. That's why I believe task #1 for nearest future is maximum performance and rock-solid stability of what we have.

Then there is the case of syscons. It's old, it's inflexible and it's mostly i386-centric. Just until recently most of our so-called embedded targets were headless so there were no pressure from this side to reorganize things. My experience with coding two framebuffer drivers or trying to add PS/2 keyboard support on non-i386 platform was not very pleasant. It's messy and there is a lot of code duplication. [newsyscons project](http://svnweb.freebsd.org/base/user/ed/newcons/) may be the way to go, I haven't looked at it yet. We just need someone(tm) to finish it and get into the tree.

Fix these two issues should make bring-up process easier. It leaves us with question of GPU support. But it's different story for different post...

Happy New Year, everybody!
