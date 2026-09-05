---
title: gpiokeys support committed
date: '2016-05-20T17:10:36'
draft: false
author: Oleksandr Tymoshenko
tags:
- evdev
- freebsd
- gpiokeys
categories:
- FreeBSD
---

To those who do not track FreeBSD commit messages: I committed gpiokeys driver to -CURRENT as [r299475](https://svnweb.freebsd.org/base?view=revision&revision=299475). The driver is not enabled in any of the kernels but can be built as a loadable module.

For now it stays disconnected from main build because it breaks some MIPS kernel configs. Configs in question include "modules/gpio" as part of MODULES_OVERRIDE variable and since gpiokeys can be built only with FDT-enabled kernel the build fails.

gpiokeys can be used as a base for more input device driver: "[gpio-keys-polled](http://lxr.free-electrons.com/source/Documentation/devicetree/bindings/input/gpio-keys-polled.txt)" and "[gpio-matrix-keypad](http://lxr.free-electrons.com/source/Documentation/devicetree/bindings/input/gpio-matrix-keypad.txt)". I do not have hardware to test this at the moment. If you do and you're looking for small FreeBSD project to work on - here you go.

Next step on my ToDo list is to try tricking people into committing [evdev patch](https://wiki.freebsd.org/SummerOfCode2014/evdev_Touchscreens), which at the moment is the only requirement for unlocking touchscreen support.
