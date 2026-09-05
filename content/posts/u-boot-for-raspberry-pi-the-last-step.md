---
title: U-Boot for Raspberry Pi, the last step
date: '2012-06-22T00:28:35'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Yes, the last step. It doesn't mean that I'm abandoning this project. Not at all. It just that it has reached the state I wanted it to reach from the very beginning: fairly stable, flexible  u-boot distribution suitable to be used as an environment for OS bring-up. So now it's time to get back to the original idea: get FreeBSD running on Raspberry Pi. 

So, current state of affairs is [raspberry-pi-uboot-20120621.tar.gz](http://people.freebsd.org/~gonzo/arm/rpi/raspberry-pi-uboot-20120621.tar.gz). It includes:

- USB  support
- SD card support (FAT filesystem)
- Support for built-in USB ethernet
- Autoimport environment from uEnv.txt
- Autorun of boot script (boot.scr)

Known problems:

- two **usb start** in row without power cycle does not work
- USB transaction errors when USB device is plugged into R-Pi
- **bootm** command is not properly tested

Of course there is place for improvements and there will be some bugfixes for this branch. I'll prepare USB driver (and, probably, SDHC) for upstream but it will be background activity and my primary objective for nearest future will be FreeBSD on R-Pi.
