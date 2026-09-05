---
title: Color themes support^Whack for vt(4)
date: '2017-08-19T21:16:08'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

I was updating my laptop to the latest HEAD today and noticed that my bash prompt looks ugly in default console color scheme. So what with one thing and another I ended up writing color themes support for vt(4). Just because it was fun thing to do. The idea is that you can redefine any ANSI color in console using variable in /boot/loader.conf, i.e.:

```
kern.vt.color.0.rgb="0,0,0" # color 0 is black
# or
kern.vt.color.15.rgb="#ffffff" # color 15 is white
```

Here is how my Tomorrow Night theme looks like:

```
kern.vt.color.0.rgb="#1d1f21"
kern.vt.color.1.rgb="#d77c79"
kern.vt.color.2.rgb="#c2c77b"
kern.vt.color.3.rgb="#f4cf87"
kern.vt.color.4.rgb="#93b2ca"
kern.vt.color.5.rgb="#c0a7c7"
kern.vt.color.6.rgb="#9ac9c4"
kern.vt.color.7.rgb="#d0d2d1"
kern.vt.color.8.rgb="#d0d2d1"
kern.vt.color.9.rgb="#d77c79"
kern.vt.color.10.rgb="#c2c77b"
kern.vt.color.11.rgb="#f4cf87"
kern.vt.color.12.rgb="#93b2ca"
kern.vt.color.13.rgb="#c0a7c7"
kern.vt.color.14.rgb="#9ac9c4"
kern.vt.color.15.rgb="#ffffff"
```

It works only with framebuffer-based console like efifb or i915kms.
Patch: [console-color-theme.diff](https://people.freebsd.org/~gonzo/patches/console-color-theme.diff)
