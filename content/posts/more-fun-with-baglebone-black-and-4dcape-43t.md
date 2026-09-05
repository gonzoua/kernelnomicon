---
title: More fun with Beaglebone Black and 4DCAPE-43T
date: '2015-10-09T22:46:17'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

I like Qt. It runs on everything. More than 10 years ago you could run it on Linux/ARM on [Sharp Zaurus](https://en.wikipedia.org/wiki/Sharp_Zaurus) and now you can run it on [FreeBSD/Pi](http://kernelnomicon.org/?p=461). I thought it would look neat on LCD screen on BBB and coded small demo player ([qt-demo-player sources](https://github.com/gonzoua/qt-demo-player)) just for the fun of it. Stock Qt does not have FreeBSD framebuffer support so I had to hack it up ([qt-platform-scfb sources](https://github.com/gonzoua/qt-platform-scfb)). Also it seems they still consider FreeBSD/clang second class citizen comparing to FreeBSD/gcc which causes some minor POSIX-related incompatibility fallout. But other than that it was smooth sailing, patch against vendor tree is really small. I'll post it later along with build instructions. In addition to scfb platform support Qt console input plugins required: [qt5-bsd-input](https://github.com/gonzoua/qt5-bsd-input)

Demo was built using cross-compiled Qt 5.5.1, audio support is gstreamer Qt plugin. 
https://youtu.be/gyYbv0tLq9o
