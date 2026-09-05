---
title: FreeBSD on RPi 2 progress
date: '2015-11-09T15:04:20'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Quick update on progress in FreeBSD's support of Raspberry Pi:
- VCHIQ driver was updated to the latest vendor code and bunch of FreeBSD-specific problems was fixed: locking, handling of non-cacheline aligned data
- Raspberry Pi userland code was updated to the latest vendor code
- Mikael Urankar created [misc/raspberrypi-userland](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=204388) for userland libraries/utilities
- Mikael also created [multimedia/omxplayer](https://bugs.freebsd.org/bugzilla/show_bug.cgi?id=204392) port for OMXPlayer, video player developed for RPi. It's also used in Kodi player on Pi.
- I created [misc/ioquake-pi](https://github.com/gonzoua/experimental-freebsd-ports/tree/master/games/ioquake3-pi) port to make it easier for people to try it out

I put together all this stuff on my brand new Pi 2 and recorded demo that showcases, OpenGL, Quake3, omxplayer, camera, and audio. I hope all these ports will be committed/updated before next round of armv6 packages build.

https://youtu.be/NYlvrr6cOpY
