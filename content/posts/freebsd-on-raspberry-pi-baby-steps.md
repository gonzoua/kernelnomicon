---
title: 'FreeBSD on Raspberry Pi: baby steps'
date: '2012-05-21T20:47:43'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Thanks to Robert Watson I got my hands on brand new raspberry pi device (model B) and now trying to get FreeBSD running on it. That turned out to be non-trivial task. For one - there is no way to netboot the board. At least I failed to find it. It seems that initial Linux bring-up was done on evaluation modules with proper bootloader, JTAG support and whatnot and for public use limited boot loader was released. So instead porting FreeBSD on it I face the task of porting U-Boot. Which is not something entirely impossible. 

CPU support is already there. USB ethernet used in R-Pi is very similar to the one that is used on PandaBoard. So the major task is to add DWT OTG driver to U-Boot. But we'll do it step by step. 

Step 1: Boot sequence.

Stock boot loader consists of three binary blobs: bootcode.bin, loader.bin, start.elf. I didn't find exact description of boot process but what I figured so far: they're not ARM binaries, they're ran on GPU. start.elf is responsible for splitting memory between GPU and ARM core. There several versions of it, the only difference is amount of memory allocated for GPU. Details of the GPU part of the boot process are not essential. What is essential is that all these blobs are there to fetch kernel.img from boot partition, place it at physical address 0x00000000 and pass control to this address. The rest is up to instructions in the kernel.img file.

Step 2: Hello world from U-Boot.

After some hacking I finally got U-Boot printing "PI-BOOT" from arch_cpu_init. And ofter that it hangs somewhere in serial ports init function. Fun, fun, fun. Git repo: [https://github.com/gonzoua/u-boot-pi/tree/rpi](https://github.com/gonzoua/u-boot-pi/tree/rpi) (rpi branch). 

Building:
`make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi- raspberry_pi_config
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabi-
`

Looks like boot loader does not like overwriting kernel.img so I created small script that just creates boot partition from scratch: [rpi-deploy.sh](https://github.com/gonzoua/u-boot-pi/blob/rpi/rpi-deploy.sh). I use it on linux and there are some hardcoded values but it should give you the general idea of what needs to be done for deployment. 

More steps to follow.
