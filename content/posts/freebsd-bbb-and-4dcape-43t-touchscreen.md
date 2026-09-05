---
title: 'FreeBSD, BBB, and 4DCAPE-43T: touchscreen'
date: '2015-10-16T19:42:08'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

And yet another demo of 4DCAPE-43T, this time it's touchscreen. On AM335x SoC touchscreen controller is coupled with analog-to-digital converter, for which there is a driver in FreeBSD HEAD: ti_adc. I had to implement touchscreen part and add driver  userland communication protocol. For proof of concept I used significantly dumbed-down version of [Linux input event protocol](http://lxr.free-electrons.com/source/Documentation/input/input.txt#L262). tslib serves as a userland part of the demo. I believe it's de-facto standard for touchscreen devices interface in Linux world. Only two things were changed comparing to stock one: I added bsd-raw  input plugin to communicate with kernel and rewrote framebuffer-related routines.

Code is in [4dcape-43t branch](https://github.com/gonzoua/freebsd/tree/4dcape-43t) in my work-in-progress GitHub repo and in [freebsd branch](https://github.com/gonzoua/tslib/tree/freebsd) of tslib fork. 

https://youtu.be/8aPUuMG3G9o
