---
title: Raspberry Pi UART0 default frequency
date: '2016-12-14T14:46:02'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

If you're trying to boot FreeBSD with latest RaspberryPi firmware - be aware that [this commit](https://github.com/raspberrypi/firmware/commit/d0bc6ce8e2ae7850959fed4edb0695f3cddfb96a) changed default frequency for UART0 on at least RaspberryPi 2, so to get serial console working in u-boot/ubldr again you need to add this line to config.txt:

```
init_uart_clock=3000000
```
