---
title: VCHIQ drivers work again
date: '2013-01-13T19:19:10'
draft: false
author: Oleksandr Tymoshenko
categories:
- ARM
- FreeBSD
- Raspberry Pi
---

I synced both [vchiq-freebsd](https://github.com/gonzoua/vchiq-freebsd) and [userland](https://github.com/gonzoua/userland) to latest and greatest. 

As I mentioned earlier - OS compatibility shim was removed from upstream sources so I had to create Linux KPI implementation layer which turned out not that awful task because I managed to reuse a lot of code from Max Khon's [DAHDI port](http://freebsdfoundation.blogspot.com/2010/10/update-on-dahdi-project.html). I had to implement (in somewhat hackish fashion) kthread API, re-implement semaphores support using condvar and mutex in order to get _interruptible part of API working properly and create dumb implementation of rather small subset of Linux list.h API. 

With latest code I got pretty much all demos in hello_pi working except hello_jpeg(crashes system) and hello_encode(didn't test). The most exciting bit for me was watching H.264 video playing on Raspberry Pi in hello_video demo. Network throughput still sucks so I had to copy file to tmpfs partition in order to get smooth playback though. 

If you want to test VCHIQ - in addition to sources you'll need latest [firmware files](https://github.com/raspberrypi/firmware/tree/master/boot). For demos you'll also have to install freetype2 and manually hack Makefile.include in hello_pi. I'm planning to create ports/packages for both drivers and userland some time next week. 

On the related note: Aleksandr Rybalko [got XOrg working on Efika MX Smartbook](http://raybsd.blogspot.com/2013/01/hope-you-like-it.html) so FreeBSD/Pi will get graphic interface soon :)
