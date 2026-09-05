---
title: 'Netbooting ARM/MIPS devices: ubldr'
date: '2013-04-19T19:24:19'
draft: false
author: Oleksandr Tymoshenko
tags:
- arm
- netboot
categories:
- Uncategorized
---

As it was mentioned in [previous post](http://kernelnomicon.org/?p=327) U-Boot can boot FreeBSD kernel directly but this approach doesn't allow a great deal of control over boot process: there is no way to set tunables' values or pre-load module. Controlling this stuff requires more knowledge of FreeBSD internal data structures and its boot process then U-Boot holds.  

On i386 and other Tier1 architectures this task is handled by the [loader(8)](http://www.freebsd.org/cgi/man.cgi?query=loader&sektion=8) program. It's last stage boot loader (e.g. it's supposed to pass control to FreeBSD kernel only), highly customizable and scriptable. [loader(8)](http://www.freebsd.org/cgi/man.cgi?query=loader&sektion=8) relies on one of the previous stages boot loader to access resources like disks, console, network. For i386 it's BTX and BIOS. 

ubldr is implementation of loader(8) for ARM on top of U-Boot. Original code was developed by Semihalf back in 2008 and has been being improved by them and FreeBSD Community since then. Despite it has been around for almost 5 years amount of documentation is shockingly low. I found only [these slides](http://www.bsdcan.org/2008/schedule/attachments/49_2008_uboot_freebsd.pdf) from BSDCan 2008.

One of the nice feature U-Boot provides is API for stand-alone process. If you don't need full-blown operating system running on your hardware but still want access to SD card/network/console you can request them from U-Boot via syscall-like API that turns boot loader into quasi-OS. Some bits of information on this topic can be found in api/README file in U-Boot sources: [here](http://git.denx.de/?p=u-boot.git;a=blob_plain;f=api/README;hb=HEAD). 

ubldr uses U-Boot API to enumerate devices that might be used as a boot source: block (e.g. SD card) or network. For network device it will use BOOTP to try to obtain network/boot data and then mount directory over NFS. For block device it will inspect partition table and try to find suitable partition to use as a root device. Once root is mounted ubldr will perform standard loader(8) magic: get loader-related config from /boot/ directory and act on it. 

There is no dedicated top-level build target for ubldr so getting it compiled is a little bit tricky. You need to perform whole buildworld cycle  before compiling ubldr. Build script would look something like this:

```
export SRCROOT=/src/FreeBSD/head
export MAKESYSPATH=$SRCROOT/share/mk
export TARGET=arm
export TARGET_ARCH=armv6
export MAKEOBJDIRPREFIX=/src/FreeBSD/obj

make -C $SRCROOT buildworld

buildenv=`make -C $SRCROOT buildenvvars`

eval $buildenv make -C $SRCROOT/sys/boot clean
eval $buildenv make -C $SRCROOT/sys/boot obj
eval $buildenv make -C $SRCROOT/sys/boot UBLDR_LOADADDR=0x2000000 all
```

Meaning of UBLDR_LOADADDR is the same as KERNPHYSADDR  in [previous post](/?p=351&preview=true).

ubldr is ELF executable and can be used with bootelf command. Typical boot log is something like this:

```
## Starting application at 0x02000054 ...
Consoles: U-Boot console  
Compatible API signature found @7b662a8
Number of U-Boot devices: 2

FreeBSD/armv6 U-Boot loader, Revision 1.2
(gonzo@bsdbox, Fri Apr 19 18:52:33 PDT 2013)
DRAM:    128MB

Device: disk

Device: net

/boot/kernel/kernel data=0x3ae624+0x2128c syms=[0x4+0x71ca0+0x4+0x44075]
Hit [Enter] to boot immediately, or any other key for command prompt.
Booting [/boot/kernel/kernel]...               
Waiting for Ethernet connection... done.
Using DTB provided by U-Boot.
Kernel entry at 0x100100...
Kernel args: (null)
KDB: debugger backends: ddb
KDB: current backend: ddb
Copyright (c) 1992-2013 The FreeBSD Project.
Copyright (c) 1979, 1980, 1983, 1986, 1988, 1989, 1991, 1992, 1993, 1994
        The Regents of the University of California. All rights reserved.
FreeBSD is a registered trademark of The FreeBSD Foundation.
...
```
