---
title: U-Boot for Raspberry Pi, step 2
date: '2012-05-26T00:36:52'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

I had some issues with console output from U-Boot on my R-Pi but Alexander Rybalko submitted patch that seems to fix them. One more issue that needed to be resolved: mysterious hangs in relocate_code. Some digging revealed that system hangs once PC reaches address 0x100. Some more digging yielded that at this address is used by GPU part of loader to store arguments for kernel.

So after tweaking .text offset for u-boot and generating proper image using [this tool](https://github.com/raspberrypi/tools/tree/master/mkimage) I finally got:

```
monitor len: 0001A14C
ramsize: 08000000
Top of RAM usable for U-Boot at: 08000000
Reserving 104k for U-Boot at: 07fe5000
Reserving 1216k for malloc() at: 07eb5000
Reserving 32 Bytes for Board Info at: 07eb4fe0
Reserving 120 Bytes for Global Data at: 07eb4f68
New Stack Pointer is: 07eb4f58
RAM Configuration:
Bank #0: 00000000 128 MiB
relocation Offset is: 07fdd000
memcpy(07eb4f68, 03ffff80, 120)
relocated
relocate_code(07eb4f58, 07eb4f68, 07fe5000)
WARNING: Caches not enabled
monitor flash len: 00019124
Now running in RAM - U-Boot at: 07fe5000
Using default environment

Destroy Hash Table: 07ffb58c table = (null)
Create Hash Table: N=66
INSERT: table 07ffb58c, filled 1/67 rv 07eb5264 ==> name="baudrate" value="115200"
INSERT: free(data = 07eb5008)
INSERT: done
In:    serial
Out:   serial
Err:   serial
Net:   No ethernet found.
Raspberry-Pi #
```

Input doesn't work yet though.
