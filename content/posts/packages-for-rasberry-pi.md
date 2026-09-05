---
title: Packages(*) for Rasberry Pi(**)
date: '2013-01-11T20:05:44'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

I finally got around to setting up experimental pkgng repo for ARM in order to share packages with other ARM developers and users who feel adventurous. And man, was it simple. I have pandaboard that is super-fast comparing to Raspberri Pi so I use it for building ports. There were several installed so I just had to generate packages for them using 

```
pkg create -a
```

 command. Then I uploaded all newly generated files to the server, grabbed packages [built and shared](http://lists.freebsd.org/pipermail/freebsd-arm/2012-December/004493.html) by Stephen Hurd, removed duplicates with older versions and generated repo.txz by issuing 

```
pkg repo
```

 command. 

Then on a raspberry pi I created pkg.conf in which I pointed to my newly created "repo", updated metada and installed git:

```
# echo 'PACKAGESITE: http://people.freebsd.org/~gonzo/arm/pkg/' > /usr/local/etc/pkg.conf
# pkg update
# pkg install git
```

Pi took some time to push files back and forth over NFS (I use NFS root on my devices) but eventually I got git with all dependencies up and running.

**!!! Please note that packages are not officially provided by FreeBSD Project. They're only for experimental purpose so install them at your own discretion !!!**

Thanks to bapt@ for working on this great tool. 

<sup>*</sup> - some
<sup>**</sup> - And for other ARM devices
