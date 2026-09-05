---
title: Raspberry Pi console
date: '2013-06-24T18:47:32'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Quick hint. If you did not disable **"device sc"** in kernel config all the message from kernel go to video console. But if something bad happened after kernel started and before framebuffer driver is activated all you'll see would be **"Kernel args: (null)"** message on serial console which is not very helpful. So in order to debug this problem and have kernel boot messages on both monitor and serial port without recompiling kernel just add following line to /boot/loader.rc on SD card: 

```
set boot_multicons="YES"
```
