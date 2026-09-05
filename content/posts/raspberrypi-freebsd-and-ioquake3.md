---
title: RaspberryPi, FreeBSD and ioquake3
date: '2015-01-07T15:37:55'
draft: false
author: Oleksandr Tymoshenko
tags:
- freebsd
- raspberry pi
- vchiq
categories:
- ARM
- Raspberry Pi
---

**Update**: [support for keyboard/mouse](http://kernelnomicon.org/?p=455) has been added

After New Year I got back to hacking the VCHIQ stuff (thanks to adrian@ for prodding). Since last time I touched NetBSD folks got it merged to main tree, syncing with latest upstream code and fixing some stupid bugs in my codebase. So I partially merged things back, spent some time on fixing more bugs introduced by yours truly, merged userland bits from latest Broadcom's bits (and fixing some bugs introduced by them). And as a result VCHIQ got stable enough to run ioquake3d on raspberry pi. Well, you can't play it because there is no sound and no mouse support and keyboard support is severely crippled but you can navigate menus and watch demoes. 

Here is short summary of how to get it running:

- Get latest HEAD that includes r276794
- Get latest [crochet-freebsd](https://github.com/kientzle/crochet-freebsd)
- Create configuration file for RasspberryPi, make sure that it's configured for 2Gb SD card and has VideoCore enabled. i.e. it contains:  

```
option ImageSize 1950mb # for 2 Gigabyte card
option VideoCore
```
- Build RPi image and flash to SD card
- mount FreeBSD partition, e.g. `mount /dev/mmcsd0s2a /mnt`
- Copy Quake3 PAK files to /baseq3 directory on SD card
- Download [http://people.freebsd.org/~gonzo/arm/ioquake3.arm.tar.gz](http://people.freebsd.org/~gonzo/arm/ioquake3.arm.tar.gz) and copy *.so files to /baseq3 and ioqake3.arm to /usr/bin on SD card 
Unmount FreeBSD partition and mount boot partiotion, e.g. `mount_msdosfs /dev/mmcsd0s1 /mnt`
- Edit config.txt and change gpu_mem value to 64
- Unmount SD card and boot it on your Pi
- Load vchiq module: `kldload vchiq`
- Start Quake3: `ioqake3.arm +set s_initsound 0`

Keyboard support is really broken. TAB and ENTER works, so you can navigate menus. But that's pretty much it. 

ioquake3 codebase with my minor changes located here: [https://github.com/gonzoua/quake3](https://github.com/gonzoua/quake3)
I provide pre-compiled binaries because for some reason ioquake3 built with xdev tools crash in qsort (libc incompatibilities?) so I use `make buildenv` to build it. 

And here is photo of demo in action (there are RaspberryPi and ZedBoard on it too, yay!)

[![Photo Jan 07](/uploads/2015/01/Photo-Jan-07-1024x768.jpeg)](/uploads/2015/01/Photo-Jan-07.jpeg)
