---
title: Qt5 for FreeBSD/Pi
date: '2015-01-14T20:26:55'
draft: false
author: Oleksandr Tymoshenko
tags:
- freebsd
- qt5
- raspberry pi
- vchiq
categories:
- FreeBSD
---

Build SD card image using crochet-freebsd with `option VideoCore` enabled. Mount either SD card itself of image to build host

```
mount /dev/mmcsd0s2a /pi
```

Checkout Qt5 sources and patch them  

```
cd /src
git clone git://gitorious.org/qt/qt5.git qt5
cd qt5
git checkout 5.4.0
MODULES=qtbase,qtdeclarative,qtgraphicaleffects,qtimageformats,qtquick1,qtquickcontrols,qtscript,qtsvg,qtxmlpatterns
./init-repository --module-subset=$MODULES

fetch -q -o - http://people.freebsd.org/~gonzo/arm/rpi/qt5-freebsd-pi.diff | patch -p1
```

Configure, build and install Qt5 to SD card

```
./configure -platform unsupported/freebsd-clang -no-openssl -opengl es2 -device freebsd-rasp-pi-clang -device-option CROSS_COMPILE=/usr/armv6-freebsd/usr/bin/ -sysroot /pi/ -no-gcc-sysroot -opensource -confirm-license -optimized-qmake -release -prefix /usr/local/Qt5 -no-pch -nomake tests -nomake examples -plugin-sql-sqlite

gmake -j `sysctl -n hw.ncpu`
sudo gmake install
```

You need BSD-specific plugins to enable mouse and keyboard input in EGLFS mode 

```
cd /src/
git clone https://github.com/gonzoua/qt5-bsd-input.git
cd qt5-bsd-input
/src/qt5/qtbase/bin/qmake
gmake
sudo gmake install
```

Build application you'd like run and install it. I use one of the examples here

```
cd /src/qt5/qtbase/examples/opengl/cube
/src/qt5/qtbase/bin/qmake
gmake
sudo gmake install
```

Unmount SD card, boot Pi, make sure vchiq is loaded

```
root@raspberry-pi:~ # kldload
```

Start application 

```
root@raspberry-pi:~ # /usr/local/Qt5/examples/opengl/cube/cube -plugin bsdkeyboard -plugin bsdsysmouse
```

If you see something like this:

```
EGL Error : Could not create the egl surface: error = 0x3003
```

Or this:

```
QOpenGLFramebufferObject: Framebuffer incomplete attachment.
```

It means you need to increase GPU memory by setting `gpu_mem` in `config.txt`. Amount depends on framebuffer resolution. 128Mb works for me on 1920x1080 display.

`bsdsysmouse` plugin uses `/dev/sysmouse` by default, so you either should have moused running or specify actual mouse device, e.g.:

```
root@raspberry-pi:~ # cube -plugin bsdkeyboard -plugin bsdsysmouse:/dev/ums0
```

`bsdkeyboard` uses STDIN as input device, so if you're trying to start app from serial console it should be something like this:

```
root@raspberry-pi:~ # cube -plugin bsdkeyboard -plugin bsdsysmouse < /dev/ttyv0
```
