---
title: R-Pi Boot process
date: '2012-06-04T20:12:12'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Just a note to myself to keep useful info. 
Bakul Shah in freebsd-arm quotes R-Pi forum, but I failed to find original. 

> 1. Stage 1 boot is in the on-chip ROM. Loads stage2 in the L2 cache!
> 2. Stage 2 is bootcode.bin. Enables SDRAM and loads stage3
> 3. Stage 3 is loader.bin.  Knows about elf format and load start.elf
> 4. start.elf loads kernel.img (+ it is the main gpu code).
> It reads config.txt, cmdline.txt and bcm2835.dtb
> If the dtb file exists, it is loaded at 0x100 & kernel @ 0x8000
> Else if disable_commandline_tags is set load kernel @ 0x0
> Else if load kernel @ 0x8000 and put ATAGS at 0x100
> See Issue 16 in https://github.com/raspberrypi/linux/issues
> 5. kernel.img, is the *first* thing that runs on the ARM
> processor.

And more information on config.txt [here](http://elinux.org/RPi_config.txt)
