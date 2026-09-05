---
title: Jetson TK1, FreeBSD, and SSD
date: '2016-08-19T13:30:39'
draft: false
author: Oleksandr Tymoshenko
tags:
- freebsd
- jetson tk1
- nvidia
categories:
- FreeBSD
---

Looks like my attempt to cheap out on SSD for TK1 has backfired. I went for the cheapest SSD available in local store (Toshiba Q300) but when I tried to checkout FreeBSD sources to the drive I got bunch of WRITE_FPDMA_QUEUED timeouts and system locked up. The same thing happened when I tried to perform checkout on Linux. The drive itself was OK, it survived "svn co .../head" and dd when connected using USB-to-SATA adapter. 

I believe the problem was that TK1 SATA voltage was out of Toshiba's tolerance range. I replaced Q300 with Samsung EVO 850 and was able to checkout sources and finish buildworld using SSD for src/obj storage.
