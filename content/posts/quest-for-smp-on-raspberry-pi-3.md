---
title: Quest for SMP on Raspberry Pi 3
date: '2016-11-24T10:58:06'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Getting SMP support for RPI3 took some time and was interesting learning experience. So I share bits of what I learned here.

FreeBSD/arm boot start on one CPU (called primary) the rest of CPUs are "on hold". At some point in boot sequence non-primary CPUs are forced to call mpentry() function. The way CPU is forced to call mpentry is platform-dependent. RPi2 for instance passes mpentry and argument to VideoCore using mailbox interface and then VideoCore kicks ARM CPU in action. So most of SoC has their own implementation of platform_mp_start_ap platform API method. 

FreeBSD/arm64 takes more standardized approach. It relies on secure monitor to provide this functionality. To explain what secure monitor is I need to diverge a bit into Aarch64 architecture. Aarch64 has four Exception Levels: EL3, EL2, EL1, EL0. Higher level acts as a "kernel" to the lower level. Most usual roles for exception levels are:

EL3 - Secure Monitor
EL2 - Hypervisor
EL1 - OS kernel
EL0 - userland apps

Just like userland can request kernel service through syscal lower exception level can request service from higher one through syscall-like mechanism. On Aarch64 it's SMC, HVC, SVC instruction to get to EL3, EL2, EL1 respectively. There are multiple services that EL1  can request from Hypervisor or Secure Monitor but one of them is PSCI (Power State Coordination Interface). It's an API to control CPUs and power states of the system. Full spec can be found [here](http://infocenter.arm.com/help/index.jsp?topic=/com.arm.doc.den0022c/index.html).

SMP support on FreeBSD/arm64 requires Secure Monitor and there is no such thing for Raspberry Pi 3. Normally it's a piece of software that is provided as a part of SoC. Stage 1 boot loader resides in secure ROM and every subsequent boot stage can be verified using certificates. There are regions of memory where secure monitor is loaded that can not be accessed from non-secure exception levels. No such thing on RPi3 either.

There were 3 options to get PSCI on RPi3: write "bare metal" implementation from the scratch, add it to U-Boot or port existing open source secure monitor to RPi3. Since U-Boot was already a part of the setup I looked up if there was PSCI support for it. There was for ARMv7 in the mainline and for AMRv8 as a patchset with generic PSCI framework and implementation for one of the platforms. Framework relied on the existence of secure memory region which is not a thing on RPi3 so I went for another option: port existing monitor.  

Reference implementation of SM is [ARM Trusted Firmware](https://github.com/ARM-software/arm-trusted-firmware). It's very well documented and easily extendable but the problem was that just like with U-Boot it made assumptions about memory layout that were not true for RPi3 and were not easy to change in the ATF code. 

At this point bare metal PSCI emulation looked like less work than the other two options and I started experimenting with really low level stuff. I will write my progress up in the next blog post.
