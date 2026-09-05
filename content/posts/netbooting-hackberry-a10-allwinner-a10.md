---
title: Netbooting Hackberry A10 (Allwinner A10)
date: '2013-02-10T00:14:34'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Last few weeks I've been acting as a reviewer for Ganbold Tsagaankhuu's port of FreeBSD for Cubieboard so in order to provide more valuable input and less naysaying I decided to get A10-based device to test his changes. So I ordered [Hackberry](https://www.miniand.com/products/Hackberry%20A10%20Developer%20Board) from miniand.com. I'm not great fan of pushing SD cards back and forth so first thing I do with my SoCs is get them netbooting. That's where fun begins.

Long story short - I had to get latest [official u-boot](https://github.com/linux-sunxi/u-boot-sunxi), merge network driver (sunxi_wemac) from [hno's u-boot](https://github.com/hno/uboot-allwinner) and add some GPIO magic to emac initialization. Namely - configure pin H19 (emac_power) as function 1 and set its value to 1. Only then I got net-related commands working properly.

If you're interested in building your own bootable SD card: [fetch these files](http://people.freebsd.org/~gonzo/arm/hackberry/). uEnv.txt and boot.scr are tailored to my needs so you might want to change them. mksd.sh does all the job, just make sure you use proper device name for SD card.
