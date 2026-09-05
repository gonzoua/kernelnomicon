---
title: 'Work in Progress: LCD driver for AM335x evaluation module'
date: '2013-05-05T17:25:57'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

I'm trying to wrap up some project I started working on quite some time ago and this is first chunk of clean-up.  

[Patch](http://people.freebsd.org/~gonzo/arm/patches/am335x-pwm-lcd.diff)contains:

- Kernel config for AM335x EVM
- dts file for AM335x EVM with TFT panel info
- LCD controller driver with some functionality missing: only 24/32 bit depth and only TFT mode is supported
- Really simple PWM driver. LCD backlight is controlled through eCAS submodule of PWMSS0 module.

I tested it only on evaluation module, although I think with proper panel/pinmux configuration it should work with BeagleBone's LCD caps too. 
Parts missing: adjusting clock to proper pixel frequency, proper allocation of framebuffer memory.
