---
title: Qt 5.6 is here and it runs on FreeBSD/Pi
date: '2016-03-20T13:11:32'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Qt 5.6 is finally out so I thought I'd give it a spin on my Raspberry Pi. Previously I used cross-compilation but this time I thought I'd spend some time in trying to create ports Qt modules. There is Qt 5.5.1 in ports and it's nicely split into sub-ports and most of gory details are hidden in bsd.qt.mk library. The problem with it is it's highly coupled with Xorg stuff and I didn't find easy way to squeeze non-desktop use cases into current infrastructure. So I just created new [custom devel/qt56 port](https://github.com/gonzoua/experimental-freebsd-ports). 

In order to get it done as fast as possible I took several shortcuts: all the stuff is installed to /usr/local/qt5 directory, there is no meta-port for submodules to share common part yet. Also besides base the only module I packaged (I was particularly interested in it) was QtMultimedia. Should be fairly easy to fix last 2 items though.

Qt layer for UI provider is called QPA: Qt Platform Abstraction. There are quite a few of them but I am familiar and interested in two: plain framebuffer and eglfs. Plain framebuffer stock QPA plugin is called linuxfb and naturally we can't use it for FreeBSD. Luckily there is a lot of similarities between Linux fb and syscons(or vt) fb (you can't get very innovative with framebuffer) so writing QPA support for scfb was easy. It can be used with any generic SoC with framebuffer support: AM335x(beaglebone black), i.MX6(Wandboard), NVIDIA Tegra, Pi. 

elgfs is full-screen OpenGL mode. OpenGL implementation depends on SoC vendor, e.g. Pi's library is provided by raspberrypi-userland port. If we had OpenGL support for AM335x it would have been provided by PowerVR userland libraries. As far as I understand eglfs can't be made universal: each implementation has its own quirks so you have to specify target OpenGL vendor during build time. So far we support eglfs only for Raspberry Pi thanks to Broadcom's open-sourcing kernel drivers and userland libraries. 

Then there is question of input. When you start your application from console you have several options to get user's input: keyboard, mouse, touchscreen. Common way to do this on Linux is through evdev which is a universal ways to access these kinds of devices. There is effort to get this functionality on FreeBSD so when it's there stock input plugins could be used as-is. Until then there are two non-standard plugins by yours truly: bsdkeyboard and bsdsysmouse. 

scfb, bsdysmouse, and bsdkeyboard are included in experimental ports as patches. 

All in all experience of getting Qt 5.6 running on FreeBSD/Pi was smooth. And to make this post more entertining here are two demos running on my Pi2 with official touchscreen.

Qt demo player with visualizer ([src](https://github.com/gonzoua/qt-demo-player)):
https://youtu.be/OlZmzSgMKOo

OpenGL demo with Qt logo in it ([src](https://github.com/gonzoua/freebsd-logo-gl)):
https://youtu.be/3-sdJG9wA3k
