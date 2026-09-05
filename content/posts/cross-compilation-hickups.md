---
title: Cross-compilation hiccups
date: '2012-12-01T14:01:36'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Good news and bad news. Let's start with good ones.

Daisuke Aoyama tracked down what causes "Unrecognized filesystem type" error with some SD cards. It is U-Boot using High Speed mode. Root cause is still unknown but as a workaround I just disabled HS mode for SD card in u-boot and updated freebsd-uboot-20121129.tar.gz. Or alternatively you can get [uboot-nohs.img](http://people.freebsd.org/~gonzo/arm/rpi/uboot-nohs.img) and use it to replace uboot.img on your SD card.

Bad news are: installworld for cross-compiled FreeBSD is broken unless you're doing it on the latest HEAD. The reason is utility called mtree(8). It is used to ensure that target filesystem permissions and owners/groups are correct. Owners and groups are described as usernames and group names, not as numeric UIDs/GUIDs and mtree uses getpwXXX family of routines to convert names to numeric values. See the problem already? If new system user is added to latest HEAD and you use old trusty FreeBSD 9.0, there is no way mtree would know about this user. NetBSD solved this problem by introducing -N command-line option that lets you point mtree to the **target** system's master.passwd and groups. So we need to port this feature to FreeBSD in order to get proper cross-compilation environment. And that's my plan for next few days. 

So if you see something like this:

```
mtree -eU  -f /src/FreeBSD/head/etc/mtree/BSD.var.dist -p /mnt/var
mtree: line 22: unknown user auditdistd
*** [distrib-dirs] Error code 1
```

Either update to latest HEAD, use mergemaster -p or wait couple of days.
