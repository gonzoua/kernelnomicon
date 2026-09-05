---
title: 'WIP: Ubiquity''s router station'
date: '2009-02-26T21:30:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- Flyswatter
- FreeBSD
- JTAG
- MIPS
- RouterStation
---

So it has been a month since last post about this device and I think it's time to announce current state of affairs.

At the moment further progress was blocked with something that looks like memory corruption. It's hard to trace with ktr(4) and printf(9) so I ordered [Flyswatter JTAG adapter](http://www.tincantools.com/product.php?productid=16134) and [MIPS14 adapter](http://www.tincantools.com/product.php?productid=16145&cat=251&page=1) from Tin Can Tools. I was warned that Flyswatter/MIPS combination is not supported by OpenOCD but I'd better spend some time making it work then tracing obscure memory corruptions in the wild.
