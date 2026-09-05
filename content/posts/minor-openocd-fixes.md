---
title: Minor OpenOCD fixes
date: '2010-07-06T03:00:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
- JTAG
- MIPS
- octeon
- openocd
---

Back from the land of GUI software. I have bought one more Flyswatter JTAG recently and now have two boards connected to my home box. Unfortunately both Flyswatters got the same USB serial number so stock openocd opens only the first device it stumbles upon. Here is [small patch](http://people.freebsd.org/~gonzo/openocd/ftdi_index.diff) that adds ft2232_index command to OpenOCD FTDI driver that allows to point at specific device to open. Works only with libftdi.  In the same directory you can find my configs for AR71XX-based RouterStation Pro and Portwell's CAM-0010 device based on Octeon CN3010
