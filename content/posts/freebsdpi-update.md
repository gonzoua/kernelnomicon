---
title: FreeBSD/Pi update
date: '2012-10-29T19:34:32'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

So, here is status update on the progress:

- freebsd-pi github repo has been merged to HEAD and should be considered only as a reference from now on. 
- I repackaged [freebsd-pi-uboot-20120806-sd.tar.gz](http://people.freebsd.org/~gonzo/arm/rpi/freebsd-pi-uboot-20120806-sd.tar.gz) so tar should not complain about uid/guid stuff
- Hans Peter Selasky and Alexander Rybalko added host mode support for DWC OTG driver
- Problem with tty on serial port has been fixed. Use 3wire.115200 type for ttyu0 in /etc/ttys 

Still a lot of stuff to do though. 
P.S.
I'll post updated build script later.
