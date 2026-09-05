---
title: USB driver for FreeBSD/Beaglebone
date: '2013-06-27T21:10:36'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

It took me two months but I finally got back to hacking on musb driver for FreeBSD (the one that is used in TI AM335x-based devices like Beaglebone or Beaglebone Black). Previous revision turned out not to be ready for production. Here is the new one: [beaglebone-usb-20130626.diff](http://people.freebsd.org/~gonzo/arm/patches/beaglebone-usb-20130626.diff). I adopted it to latest HEAD, fixed numerous bugs, added support for SPLIT transactions and USB suspend/resume signalling. There is some cleaning-up to do but unless something major comes up the plan is to commit it over next few days.
