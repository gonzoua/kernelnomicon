---
title: FDT driver skeleton generator
date: '2013-05-03T11:20:14'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Writing new driver for FDT-based device always involves several simple steps:

- writing generic newbus driver skeleton
- Checking for compatibility of node in probe routine
- Allocate memory/IRQ resources in attach routine

I can't say for other developers but I just copy existing driver, remove all device-specific stuff and rewrite generic stuff. Which is less time-consuming then writing it from scratch but time-consuming it is. Being huge fan of automation of any kind I decided to let computer do all this dumb work and leave creative part (copy-pasting registers definition from spec to code) to myself. the result is [this script](https://github.com/gonzoua/freebsd-misc/tree/master/fdt_skeleton).

Developer feeds driver description in YAML format to the script and gets driver skeleton that requires minimal amount of editing to get it compiled. Driver description includes author name, prefix for macroses, prefix for newbus method-functions, FDT compatibility string, driver name and number of IRQ/MEMORY resources. A minute saved is a minute earned. 

YAML example:

```
AUTHOR: Oleksandr Tymoshenko <gonzo@freebsd.org>
PREFIX: am335x_pwm
MACRO_PREFIX: PWM
DRIVER: am335x_pwm
FDT_COMPATIBLE: ti,am335x-pwm
IRQ_RESOURCES: 0
MEM_RESOURCES: 4
```
