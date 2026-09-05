---
title: Inky pHat on FreeBSD/Pi
date: '2018-11-14T10:21:27'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

About a month ago I purchased [Inky pHat](https://shop.pimoroni.com/products/inky-phat) from Pimoroni, Pi hat with 220x104 red and black eInk screen. The device has an SPI interface with three additional GPIO signals: reset pin, command/data pin, and busy pin. Reset and busy pins are self-explanatory: the former resets device MCU the latter signals to the Pi whether the MCU is busy handling previous command/data. Command/data signals the type of SPI transaction that is about to be sent to Inky: low means command, high - data. It more or less matches interface to SSD1306 OLED display I played with before.

There is no datasheet or protocol description, so I used [Pimoroni's python library](https://github.com/pimoroni/inky) as a reference.

To communicate with the device over SPI, you need to apply spigen device-tree overlay and load spigen driver. For Raspberry Pi 3 you probably need a patch from [this review](https://reviews.freebsd.org/D16088) applied. To load overlay add respective dtbo name to the fdt_overlays variable  in /boot/loader.conf, e.g.:

`fdt_overlays="spigen-rpi3"`

I didn't have any practical purpose for the device in mind so after several failed attempts to output RGB images in two color I ended up writing [random ornament generator](https://github.com/gonzoua/freebsd-embedded-demos/tree/master/inky_demo):

[![ornament](/uploads/2018/11/IMG_4491-300x225.jpg)](/uploads/2018/11/IMG_4491.jpg)

[![cat](/uploads/2018/11/IMG_4488-300x225.jpg)](/uploads/2018/11/IMG_4488.jpg)
