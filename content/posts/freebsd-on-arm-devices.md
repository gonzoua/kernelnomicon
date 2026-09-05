---
title: FreeBSD on ARM devices
date: '2012-04-19T18:20:25'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Damjan Marion published [nice how-to](http://people.freebsd.org/~dmarion/beaglebone/creating_bootable_sd_card/) for getting FreeBSD running on BeagleBone. Using it I managed to get my PandaBoard ES running off SD card. The only modifications to this instructions are:

- Use PANDABOARD kernel config. Stock version is configured to use NFS so it should be modified to use SD card as root.
- MLO and u-boot.bin binaries are different. I used [these](http://people.freebsd.org/~gonzo/pandaboard/). You'll need boot.scr too.

Performance is still affected by using write-through caches by default, but this issue should be fixed soon. 

boot log: 

```
U-Boot SPL 2011.09-rc2 (Oct 06 2011 - 17:56:54)
Texas Instruments OMAP4460 ES1.1
OMAP SD/MMC: 0
reading u-boot.img
reading u-boot.bin
mkimage signature not found - ih_magic = ea000014
Assuming u-boot.bin ..
reading u-boot.bin

U-Boot 2011.09-rc2 (Oct 06 2011 - 17:56:54)

CPU  : OMAP4430
Board: OMAP4 Panda
I2C:   ready
DRAM:  1 GiB
WARNING: Caches not enabled
MMC:   OMAP SD/MMC: 0
Using default environment

In:    serial
Out:   serial
Err:   serial
Net:   No ethernet found.
Hit any key to stop autoboot: 0
reading uEnv.txt

** Unable to read "uEnv.txt" from mmc 0:1 **
reading boot.scr

133 bytes read
Loaded script from boot.scr
Running bootscript from mmc0 ...
## Executing script at 82000000
reading kernel.bin

3634616 bytes read
## Starting application at 0x80200000 ...
initarm: console initialized
 arg1 mdp = 0x00000000
 boothowto = 0x00000000
 dtbp = 0xc0552060
kernel image addresses:
 kernbase       = 0xc0000000
 _etext (sdata) = 0xc04cb00c
 _edata         = 0xc05775b8
 __bss_start    = 0xc05775b8
 _end           = 0xc0595ef4
loader passed (static) kenv:
 no env, null ptr
processing avail regions:
 80000000-c0000000 -> 80000000-80200000 = 200000
 8064b000-c0000000 -> 8064b000-c0000000 = 3f9b5000
fill in phys_avail:
 region: 0x80000000 - 0x80200000 (0x00200000)
 region: 0x8064b000 - 0xc0000000 (0x3f9b5000)
KDB: debugger backends: ddb
KDB: current backend: ddb
Copyright (c) 1992-2012 The FreeBSD Project.
Copyright (c) 1979, 1980, 1983, 1986, 1988, 1989, 1991, 1992, 1993, 1994
	The Regents of the University of California. All rights reserved.
FreeBSD is a registered trademark of The FreeBSD Foundation.
FreeBSD 10.0-CURRENT #123 r234390:234445M: Wed Apr 18 20:15:54 PDT 2012
    gonzo@bsdbox:/src/FreeBSD/obj/armv6/arm.arm/src/FreeBSD/armv6/sys/PANDABOARD arm
CPU: Cortex A9-r2 rev 10 (Cortex-A core)
 Supported features: ARM_ISA THUMB2 JAZELLE THUMBEE ARMv4 Security_Ext
 WB disabled EABT branch prediction enabled
LoUU:2 LoC:1 LoUIS:2 
Cache level 1: 
 32KB/32B 4-way data cache WB Read-Alloc Write-Alloc
 32KB/32B 4-way instruction cache Read-Alloc
real memory  = 1073741824 (1024 MB)
avail memory = 1046401024 (997 MB)
Texas Instruments OMAP4430 Processor, Revision ES2.3
random device not loaded; using insecure entropy
simplebus0: <Flattened device tree simple bus> on fdtbus0
gic0: <ARM Generic Interrupt Controller> mem 0xe8241000-0xe8241fff,0xe8240100-0xe82401ff on simplebus0
gic0: pn 0x390, arch 0x1, rev 0x2, implementer 0x43b nirqs 160
l2cache0: <PL310 L2 cache controller> mem 0xe8242000-0xe8242fff on simplebus0
  L2 Cache: 1024KB/32B 16 ways
mp_tmr0: <ARM Generic MPCore Timers> mem 0xe8240200-0xe82402ff,0xe8240600-0xe82406ff irq 27,29 on simplebus0
Timecounter "ARM MPCore Timecouter" frequency 504000000 Hz quality 1000
Event timer "ARM MPCore Eventtimer" frequency 504000000 Hz quality 1000
uart0: <16750 or compatible> mem 0xe8020000-0xe8020fff irq 106 on simplebus0
uart0: console (115384,n,8,1)
ti_scm0: <TI Control Module> mem 0xd4e6a000-0xd4e6afff on simplebus0
setting internal 4 for usbb1_ulpiphy_stp
setting internal 10c for usbb1_ulpiphy_clk
setting internal 10c for usbb1_ulpiphy_dir
setting internal 10c for usbb1_ulpiphy_nxt
setting internal 10c for usbb1_ulpiphy_dat0
setting internal 10c for usbb1_ulpiphy_dat1
setting internal 10c for usbb1_ulpiphy_dat2
setting internal 10c for usbb1_ulpiphy_dat3
setting internal 10c for usbb1_ulpiphy_dat4
setting internal 10c for usbb1_ulpiphy_dat5
setting internal 10c for usbb1_ulpiphy_dat6
setting internal 10c for usbb1_ulpiphy_dat7
omap4_prcm0: <TI OMAP Power, Reset and Clock Management> mem 0xd4e6b000-0xd4e6cfff,0xd4e6d000-0xd4e6dfff,0xd4e6e000-0xd4e75fff on simplebus0
gpio0: <TI General Purpose I/O (GPIO)> mem 0xd4e76000-0xd4e76fff,0xe8055000-0xe8055fff,0xe8057000-0xe8057fff,0xe8059000-0xe8059fff,0xe805b000-0xe805bfff,0xe805d000-0xe805dfff irq 61,62,63,64,65,66 on simplebus0
gpioc0: <GPIO controller> on gpio0
gpiobus0: <GPIO bus> on gpio0
ehci0: <TI OMAP USB 2.0 controller> mem 0xd4e77c00-0xd4e77cff,0xd4e78000-0xd4e786ff,0xd4e79000-0xd4e79fff irq 109 on simplebus0
ehci0: Starting TI EHCI USB Controller
ehci0: UHH revision 0x50700100
ehci0: OMAP_UHH_SYSCONFIG: 0x00000014
ehci0: UHH setup done, uhh_hostconfig=0x8000001c
usbus0: EHCI version 1.0
usbus0: <TI OMAP USB 2.0 controller> on ehci0
iichb0: <TI I2C Controller> mem 0xe8070000-0xe80700ff irq 88 on simplebus0
iichb0: I2C revision 4.0
iicbus0: <OFW I2C bus> on iichb0
iic0: <I2C generic I/O> on iicbus0
twl0: <TI TWL4030/TWL5030/TWL60x0/TPS659x0 Companion IC> on iicbus0
twl_vreg0: <TI TWL4030/TWL5030/TWL60x0/TPS659x0 Voltage Regulators> on twl0
ti_sdma0: <TI sDMA Controller> mem 0xd4e7a000-0xd4e7afff irq 44,45,46,47 on simplebus0
ti_sdma0: sDMA revision 00010900
ti_mmchs0: <TI MMC/SD/SDIO High Speed Interface> mem 0xe809c000-0xe809cfff irq 115 on simplebus0
mmc0: <MMC/SD bus> on ti_mmchs0
Timecounters tick every 10.000 msec
usbus0: 480Mbps High Speed USB v2.0
twl0: Found (sub)device at 0x48
twl0: Found (sub)device at 0x49
twl0: Found (sub)device at 0x4a
twl0: Found (sub)device at 0x4b
ugen0.1: <Texas Instruments> at usbus0
uhub0: <Texas Instruments EHCI root HUB, class 9/0, rev 2.00/1.00, addr 1> on usbus0
mmcsd0: 1886MB <SD Memory Card> at mmc0 30MHz/4bit
Root mount waiting for: usbus0
Root mount waiting for: usbus0
uhub0: 3 ports with 3 removable, self powered
Root mount waiting for: usbus0
ugen0.2: <vendor 0x0424> at usbus0
uhub1: <vendor 0x0424 product 0x9514, class 9/0, rev 2.00/1.00, addr 2> on usbus0
Root mount waiting for: usbus0
uhub1: 5 ports with 4 removable, self powered
ugen0.3: <vendor 0x0424> at usbus0
smsc0: <vendor 0x0424 product 0xec00, rev 2.00/1.00, addr 3> on usbus0
Trying to mousmsc0: chip 0xec00, rev. 0001
nt root from ufs:mmcsd0s2 []...
miibus0: <MII bus> on smsc0
ukphy0: <Generic IEEE 802.3u media interface> PHY 1 on miibus0
ukphy0:  none, 10baseT, 10baseT-FDX, 100baseTX, 100baseTX-FDX, auto
ue0: <USB Ethernet> on smsc0
ue0: Ethernet address: 42:b5:1b:92:2f:fa
warning: no time-of-day clock registered, system time will not be set accurately
Setting hostuuid: 498c0afe-89d4-11e1-b9b6-42b51b922ffa.
Setting hostid: 0x0b54866b.
No suitable dump device was found.
Entropy harvesting: interrupts ethernet point_to_point kickstart.
Starting file system checks:
/dev/mmcsd0s2: FILE SYSTEM CLEAN; SKIPPING CHECKS
/dev/mmcsd0s2: clean, 405764 free (1988 frags, 50472 blocks, 0.4% fragmentation)
Mounting local file systems:.
Setting hostname: pandaboard.
smsc0: chip 0xec00, rev. 0001
ue0: link state changed to DOWN
Starting Network: lo0 ue0.
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> metric 0 mtu 16384
	options=3<RXCSUM,TXCSUM>
	inet 127.0.0.1 netmask 0xff000000 
ue0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=80001<RXCSUM,LINKSTATE>
	ether 42:b5:1b:92:2f:fa
	media: Ethernet autoselect (none)
	status: no carrier
Starting devd.
ue0: link state changed to UP
Starting dhclient.
Can't find free bpf: No such file or directory
exiting.
/etc/rc.d/dhclient: WARNING: failed to start dhclient
Generating host.conf.
Waiting 30s for the default route interface: .............................
Creating and/or trimming log files.
Starting syslogd.
/etc/rc: WARNING: Dump device does not exist.  Savecore not run.
ELF ldconfig path: /lib /usr/lib /usr/lib/compat
Clearing /tmp (X related).
Updating motd:.
Generating public/private rsa1 key pair.
Your identification has been saved in /etc/ssh/ssh_host_key.
Your public key has been saved in /etc/ssh/ssh_host_key.pub.
The key fingerprint is:
fc:fa:d4:6c:ca:d3:c2:c5:ff:7c:05:e1:50:0c:5f:27 root@pandaboard
The key's randomart image is:
+--[RSA1 1024]----+
|            .+E o|
|            ..oo.|
|             o.. |
|       .      o  |
|        S  .   . |
|         . oo   .|
|         .oo+.  .|
|         ++o. ...|
|        ..+o   .+|
+-----------------+
Generating public/private dsa key pair.
Your identification has been saved in /etc/ssh/ssh_host_dsa_key.
Your public key has been saved in /etc/ssh/ssh_host_dsa_key.pub.
The key fingerprint is:
cd:16:3a:62:b7:9d:b0:4d:48:c3:93:d1:4a:6a:4f:56 root@pandaboard
The key's randomart image is:
+--[ DSA 1024]----+
|        ..       |
|       ..oE      |
|       o*o.      |
|      o.+B .     |
|     .o+S =      |
|     . o.X .     |
|        o +      |
|                 |
|                 |
+-----------------+
Generating public/private rsa key pair.
Your identification has been saved in /etc/ssh/ssh_host_rsa_key.
Your public key has been saved in /etc/ssh/ssh_host_rsa_key.pub.
The key fingerprint is:
6b:03:e0:ef:28:66:01:cc:76:8a:88:70:f8:9b:a6:f2 root@pandaboard
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|o.  .            |
|++.o .           |
|*+o . . S        |
|+.o  . . .       |
|   +  . +        |
|. B  o . .       |
|o*E.. .          |
+-----------------+
Generating public/private ecdsa key pair.
Your identification has been saved in /etc/ssh/ssh_host_ecdsa_key.
Your public key has been saved in /etc/ssh/ssh_host_ecdsa_key.pub.
The key fingerprint is:
e4:aa:df:df:d4:9d:19:83:42:79:79:18:b0:b8:cc:27 root@pandaboard
The key's randomart image is:
+--[ECDSA  256]---+
|           ...   |
|          . o +  |
|        .. + + . |
|       oo o . o  |
|        SE o . o |
|       .  o .. .=|
|      .     . .o.|
|     . .   o     |
|    ... ... .    |
+-----------------+
Starting sshd.
Starting cron.
Starting background file system checks in 60 seconds.

Thu Apr 19 04:03:17 UTC 2012

FreeBSD/arm (pandaboard) (ttyu0)

login: root
Apr 19 04:03:23 pandaboard login: ROOT LOGIN (root) ON ttyu0
FreeBSD 10.0-CURRENT (PANDABOARD) #123 r234390:234445M: Wed Apr 18 20:15:54 PDT 2012

Welcome to FreeBSD!

Before seeking technical support, please use the following resources:

o  Security advisories and updated errata information for all releases are
   at http://www.FreeBSD.org/releases/ - always consult the ERRATA section
   for your release first as it's updated frequently.

o  The Handbook and FAQ documents are at http://www.FreeBSD.org/ and,
   along with the mailing lists, can be searched by going to
   http://www.FreeBSD.org/search/.  If the doc package has been installed
   (or fetched via pkg_add -r lang-freebsd-doc, where lang is the
   2-letter language code, e.g. en), they are also available formatted
   in /usr/local/share/doc/freebsd.

If you still have a question or problem, please take the output of
`uname -a', along with any relevant error messages, and email it
as a question to the questions@FreeBSD.org mailing list.  If you are
unfamiliar with FreeBSD's directory layout, please refer to the hier(7)
manual page.  If you are not familiar with manual pages, type `man man'.

Edit /etc/motd to change this login announcement.

pandaboard# uname -a
FreeBSD pandaboard 10.0-CURRENT FreeBSD 10.0-CURRENT #123 r234390:234445M: Wed Apr 18 20:15:54 PDT 2012     gonzo@bsdbox:/src/FreeBSD/obj/armv6/arm.arm/src/FreeBSD/armv6/sys/PANDABOARD  arm
pandaboard# 
```
