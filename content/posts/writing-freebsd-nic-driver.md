---
title: Writing FreeBSD NIC driver
date: '2010-02-19T06:22:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
- NIC
- driver
---

I've been through writing NIC driver 2.5 times. 0.5 was porting ADM5120 switch driver by Ruslan Ermilov and Vsevolod Lobko from NetBSD. The usual routine for this kind of thing is "take existing driver and rewrite it", e.g. copy selected parts or remove unnecessary ones. So I decided that it would be nice to skip "remove" part of procedure next time. 

All cards I had to deal with ("both" wouldn't be that impressive here) had similar design save for registers layout and some quirks. I believe that vast majority of NICs have the same design to some extent: there are circular RX/TX rings of more or less similar structure, interrupt status/mask register, media settings registers, you name it. Not a rocket science. 

So I took if_arge driver from Atheros AR71XX SoC and replaced hardware-dependent parts  with FIXME comments. Also string "ARGE" was replaced to "ADAPTER" and "arge" to "adapter" so simple s/adapter/xyz/g and s/ADAPTER/XYZ/g would give us a half-baked source base for if_xyz driver.  

It's yet to be tested whether this approach would be of any good. I'm planning to try it in next few days :) Meanwhile you can check sources [here](http://people.freebsd.org/~gonzo/if_adapter/).
