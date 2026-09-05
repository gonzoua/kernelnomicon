---
title: Multitouch support on FT5406
date: '2016-10-08T11:54:47'
draft: false
author: Oleksandr Tymoshenko
categories:
- ARM
- FreeBSD
---

Few weeks ago evdev support was finally committed to HEAD. Project [started](https://wiki.freebsd.org/SummerOfCode2014/evdev_Touchscreens) a part of SoC 2014 by Jakub Klama and then picked up, finished and submitted by Vladimir Kondratiev. It's drop-in compatible with Linux API which means all you need to do is add #ifdef __FreeBSD_ around respective includes and existing code (if it's otherwise cross-compatible with FreeBSD) should just work. Which is the case for [Qt](https://codereview.qt-project.org/#/c/172829/) and to lesser extent for [tslib](https://github.com/kergoth/tslib/pull/42). Hardware support is still moving target, FreeBSD has evdev-compatible drivers for USB keyboards, USB mice, TI's AM33xx touchscreen controller and Raspberry Pi's official touchscreen. Only the latter device supports multitouch and Vladimir submitted patch required to get it working. To my knowledge it's the first multitouch touchscreen ever working on FreeBSD so I decided to record demo to save this moment for generations to come. Well, not really. Mostly to brag and to let people know that it's possible and encourage them to make stuff and experiment with FreeBSD, ARM, and Qt. 

Demo below is standard [imagegestures example](http://doc.qt.io/qt-5/qtwidgets-gestures-imagegestures-example.html) built using latest dev branch of Qt. 

https://youtu.be/-XQk_SiaFnw
