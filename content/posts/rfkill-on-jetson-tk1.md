---
title: rfkill on Jetson TK1
date: '2016-12-11T13:50:42'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

If you're trying to install half-sized mini-PCIe wifi card in Jetson TK1, be aware, that the board has rfkill feature that is enabled by default. rfkill is hardware or software controlled switch that enables/disables RF signal on the wifi card itself. In case of mini-PCIe it's controlled by level on pin 20 of the card. On Jetson TK-1 that pin is connected to GPIO X.7 pin. So to enable wifi on the board you need to run something like:

```
gpioctl -cN gpio_X.7 OUT
gpioctl -N gpio_X.7 1
```
