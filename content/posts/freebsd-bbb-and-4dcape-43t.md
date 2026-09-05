---
title: FreeBSD, BBB and 4DCAPE-43T
date: '2015-10-04T18:49:07'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Short demo of FreeBSD running on Beaglebone Black with [4DCAPE-43T](http://www.4dsystems.com.au/product/4DCAPE_43/)

https://www.youtube.com/watch?v=4Vn_L_UzQhc

I used vendor-provided am335x-boneblack-4dcape-43t.dts file to generate dtb, you can download compiled blob [here](http://people.freebsd.org/~gonzo/arm/patches/am335x-boneblack-4dcape-43t.dtb). The system running on demo is [gpiokeys branch](https://github.com/gonzoua/freebsd/tree/gpiokeys) of my git repo: . Patch against -head is [here](http://people.freebsd.org/~gonzo/arm/patches/bbb-gpiokeys.diff). 

If you're interested only in LCD screen - it's supported by -head but you'll need to either add `device gpiobacklight` to BEAGLEBONE kernel config or enable LCD backlight manually using gpioctl: `gpioctl -f /dev/gpioc1 18 1`. gpiokeys is somewhat more complex thing and still WIP, there are some pieces missing in HEAD I had to hack around to make them work. And I haven't started research on touchscreen yet.
