---
title: 'FreeBSD on Pi: more stuff'
date: '2012-11-29T21:21:19'
draft: false
author: Oleksandr Tymoshenko
categories:
- ARM
- FreeBSD
- Raspberry Pi
---

Long overdue update on how the things are going with FreeBSD on Raspberry Pi. We've made some good progress so far:

- Hans Petter Selasky fixed low-speed interrupt endpoints problem which means we have working USB keyboard now
- GPIO driver by Luiz Otavio O Souza. So now you can blink OK LED (gpioctl -f /dev/gpioc0 -t 16). Not the most productive activity though.
- Kernel now obtains information about display resolution, memory layout, MAC address from firmware
- Framebuffer/syscons support added
- Some stability fixes for SDHCI/li>
Initial port of VCHIQ interface ([vchiq-freebsd](https://github.com/gonzoua/vchiq-freebsd))
- Port of userland libraries ([userland](https://github.com/gonzoua/userland))

 Overall stability and performance is still a problem, but it's what we're going to work on next. 

And if you missed previous post: [freebsd-pi](https://github.com/gonzoua/freebsd-pi) is no more, use HEAD from FreeBSD subversion repository. 

Boot process has been changed and now it looks like: firmware → uboot → ubldr → kernel. So old script for building image is no longer relevant. Here is [new one](http://people.freebsd.org/~gonzo/arm/rpi/build-pi-image.sh). Tim Kientzle's [scripts collection](https://github.com/kientzle/freebsd-beaglebone) for building images for BeagleBone, Pandaboard and RPi uses more systematic approach but RPi part hasn't caught up to latest boot chain changes yet. Once it is up to date I suggest using Tim's scripts. 

Building FreeBSD does not require any additional tools but if you want VideoCore bits you'll need following packages installed:

- devel/cmake
- devel/git
- devel/gmake

If you don't need VideoCore binaries, just comment build_videocore and install_videocore calls. This script will also install OpenGL ES hello_triangle demo to /root folder. To run it run perform following steps:
`
# cd /root
# kldload vchiq
# ./hello_triangle.bin
`

I tried to build Qt5 with OpenGL ES support, but build choked on compile-time assert triggered by FreeBSD using OABI. Good news though: EABI work is almost done, so there is a fat chance we'll see Qt5 with eglfs backend running on FreeBSD in near future. 

You can try pre-built [image](http://people.freebsd.org/~gonzo/arm/rpi/freebsd-pi-r243694.img.gz) (124Mb, [MD5 sums](http://people.freebsd.org/~gonzo/arm/rpi/freebsd-pi-r243694.img.sums)). Login is "root", no password. Use dd to write it to SD card. U-Boot seems to be somewhat finicky about SD cards, so if you get "** Unrecognized filesystem type **" message try another card. First boot might take some time because sshd will generate keys. U-Boot output goes to serial port and monitor, FreeBSD console messages go only to monitor, but by the end of boot sequence you should get login prompt on serial.

This image is a snapshot of work in progress and by no means a production system. 

### UPDATE

**** Unrecognized filesystem type **** U-Boot issue seems to be more widespread then I thought. I'm working on it.
