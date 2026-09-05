---
title: 'Update on ioquake/ARM for FreeBSD: controls support added'
date: '2015-01-13T21:22:33'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

I've update [ioquake binaries](http://people.freebsd.org/~gonzo/arm/ioquake3.arm.tar.gz) and pushed respective changes to github. New version has support for mouse and keyboard so you can actually play Quake3 on FreeBSD/Pi alas without sound. It should run out of the box on normal console. Make sure you have moused running or specify mouse device by setting Q_MOUSE_DEV environment variable, e.g.:

```
env Q_MOUSE_DEV=/dev/ums0 ioquake3.arm +set s_initsound 0
```
