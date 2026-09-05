---
title: 64-bit U-Boot on Raspberry Pi 3
date: '2016-10-09T16:57:35'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Short summary of couple of frustrating hours I spent trying to get my RPi3 netbooting:

U-Boot - 2016.09 
[raspberrypi/firmware/boot](https://github.com/raspberrypi/firmware/boot) - ec63df146f454e8cab7080380f9138246d877013

armstub.bin, armstub7.bin, armstub8.bin - NOT REQUIRED. There are tens of google results mentioning these files along with some dd magic - they all obsolete. Aforementioned version of firmware does not require them. 64-bit mode is controlled by arm_control variable in config.txt (see below). If  64-bit mode requested default kernel name becomes kernel8.img, and kernel load address becomes 0x80000. U-Boot uses correct default load address so no need for any additional parameters or hacks. 

Building u-boot:

```
# gmake ARCH=arm CROSS_COMPILE=aarch64-none-elf- CONFIG_EFI=y rpi_3_defconfig
# gmake ARCH=arm CROSS_COMPILE=aarch64-none-elf- CONFIG_EFI=y 
```

Boot partition:
- Copy all files/dirs from firmware/boot to your FAT partition (let's say it's mounted at /mnt/fat)
- Copy u-boot.bin to kernel8.img on your FAT partition
- Create config.txt with following content:

```
arm_control=0x200
enable_uart=1
```

If you want to keep u-boot.bin name and not use kernel8.img, add following ling:

```
kernel=u-boot.bin
```

Unmount and boot RPi3. You should see U-Boot output on both serial console and HDMI.

Default U-Boot build uses mini-uart. So if for some reason you want to use PL011 instead, patch include/configs/rpi.h and make sure that CONFIG_BCM283X_MU_SERIAL is not defined and CONFIG_PL01X_SERIAL is defined instead. 

config.txt in this case should look like:

```
arm_control=0x200
dtoverlay=pi3-disable-bt
```
