---
title: Accessing I2C and SPI from userland
date: '2016-12-17T20:29:46'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Over years I accumulated fair number of devices I have no real use for. I ordered them either on impulse, or to add couple of $$$ to the bill to get free delivery. So in order to get at least some value from those purchases I put together goofy demo using two of such devices: [I2C temperature sensor breakout](https://www.sparkfun.com/products/11931) from Sparkfun and [128x32 SPI OLED display](https://www.adafruit.com/products/661) from Adafruit. 

I wrote two libraries to talk to TMP102 (chip in which temp sensor is based) over I2C and to SSD1306(OLED display chip) over SPI and several demos simple enough to put together in one day but flashy enough to excite my inner child and bring fond memories of MSDOS days. The SPI chip requires [this fix](https://svnweb.freebsd.org/base?view=revision&revision=310170) in kernel. Userland SPI API provides only very basic functionality but luckily it was enough to talk to SSD1306.

TMP102 is I2C-only device but SSD1306 uses two signals in addition to standard SPI ones and GND/VCC: Data/Command switch and Reset. I connected them to GPIO pins 23 and 24 on my RPi. SSD1306 code is based on [Adafruit's Python library](https://github.com/adafruit/Adafruit_Python_SSD1306) and supports only one model so far, but can easily be extended to support all three of them. 

Code: [https://github.com/gonzoua/freebsd-embedded-demos](https://github.com/gonzoua/freebsd-embedded-demos)
Video:

https://youtu.be/CRHSU83VVaY
