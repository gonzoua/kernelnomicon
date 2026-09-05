---
title: singleuser on routerstation
date: '2009-05-01T05:23:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
- MIPS
- RouterStation
---

[http://people.freebsd.org/~gonzo/mips/routerstation.log](http://people.freebsd.org/~gonzo/mips/routerstation.log)

Some issues still pending though. Cache management is major one. Things seemed to work fine in emulation but backfired into face with real hardware. I ran into several "random" bugs that were narrowed down to caches. More to go :( Also ethernet driver performance is low but it's easier then hunting down ethereal cache matters. Or so I think.

This weekend will be dedicated to making openocd work with routerstation, learning debugging and profiling techniques for MIPS hardware and improving FreeBSD/MIPS pieces of DDB.
