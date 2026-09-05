---
title: 'Minnowboard Turbot: GPIO, I2C, and SPI'
date: '2017-01-06T14:35:33'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Last year I got my hands on Minnowboard Turbot (courtesy of Frank H.) and spent some time working on communications protocols support for it. Below is short summary of what works and what doesn't.

[Minnowboard Turbot](http://wiki.minnowboard.org/MinnowBoard_Turbot) is Atom-base SoC, and standard x86 part (HDMI, network, USB) FreeBSD just works on it. The board has expansion connector that exposes I2C, SPI, and GPIO pins and can be used to talk to peripheral devices. Short summary of header pins can be found on [developer.microsoft.com](https://developer.microsoft.com/en-us/windows/iot/docs/pinmappingsmbm).

## GPIO

GPIO functionality on Minnowboard provided by bytgpio(4) driver (name was chosen to match OpenBSD one). There are three banks, but the most accessible is the third one available via /dev/gpioc2. All GPIO pins on the expansion header are pin number in this bank. So to set pin "GPIO 0" on the picture to HIGH you would use `gpioc -f /dev/gpioc2 0 1`.

To use some GPIO peripheral that has FreeBSD kernel driver you will have to describe it using hints in /boot/loader.conf. For instance there is LED D2 that is attached to pin 22. So to expose it through gpioled(4) interface you'd add following lines to /boot/loader.conf:

```
gpioled_load="YES"
bytgpio_load="YES"
hint.gpioled.0.at="gpiobus2"
# pin 22: (1 << 22)
hint.gpioled.0.pins="0x400000"
hint.gpioled.0.name="D2"
```

## SPI

Driver for SPI support on Minnowboard is intelspi. Hardware supports all four SPI modes and clock frequency configuration but the driver doesn't (yet). So it's always mode 0 and frequency is hardcoded to 10MHz. To access it from userland you'd use spigen driver for now. As with gpio peripherlas, you'd have to add spigen via  hints:

```
intelspi_load="YES"
hint.spigen.0.at="spibus0"
```

There is no spigen module, so it has to be enabled in kernel config:

```
device          spibus
device          spigen
```

## I2C

I2C driver is ig4(4). There are two I2C buses, the one that is accessible from expansion header is iicbus0. As with previous two protocols devices should be defined by hints. For example:

```
ig4_load="YES"
hint.ds3231.0.at="iicbus0"
# 0x68 in 8-bit format
# for example only, hasn't been tested
hint.ds3231.0.addr="0xd0"
```

There is no ds3231 module so again, you need to enable it in kernel:

```
device          iicbus
device          ds3231
```

To use I2C from userland via /dev/iicN you'll need to load iic(4) module.
