---
title: 'U-Boot for Raspberry Pi, step 8: SDHC support'
date: '2012-06-20T12:59:32'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Initial support for SD card has just been pushed to github repo.

```
U-Boot 2012.04.01-00479-gb58d9ae-dirty (Jun 20 2012 - 11:47:13)

DRAM:  128 MiB
WARNING: Caches not enabled
MMC:   CAPS: 00000000
bcm2835_sdh: 0
Using default environment

In:    serial
Out:   serial
Err:   serial
Net:   Net Initialization Skipped
No ethernet found.
U-Boot> mmcinfo
Device: bcm2835_sdh
Manufacturer ID: 1b
OEM: 534d
Name: 00000
Tran Speed: 25000000
Rd Block Len: 512
SD version 2.0
High Capacity: No
Capacity: 1.9 GiB
Bus Width: 4-bit
U-Boot> fatls mmc 0
    16528   bootcode.bin
      127   cmdline.txt
   314691   loader.bin
  2047848   start.elf
   181196   kernel.img

5 file(s), 0 dir(s)

U-Boot>
```
