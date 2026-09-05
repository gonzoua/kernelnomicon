---
title: Flyswatter JTAG and AVR32
date: '2010-08-09T04:21:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- Flyswatter
- FreeBSD
- JTAG
- avr32
---

Today I soldered AVR32 adapter for Flyswatter JTAG. Actually it's very simple task - just connect respective pins and make common ground wire. No capacitors, resistors or MOSFETs. Idea was to make it nice and neat but having bought wrong breadboard and soldered first headers in a wrong place I just let it flow and here it is:

![](http://lh6.ggpht.com/_bTtZWrFtKsc/TF9749h_xnI/AAAAAAAAFYI/NgKmDdyq0_8/IMGP9940.JPG)

 

![](http://lh3.ggpht.com/_bTtZWrFtKsc/TF975cHy0SI/AAAAAAAAFYM/Uy6SNPtWw8Q/IMGP9941.JPG)

From aesthetics point of view suck, but it also works and it's everything I need:

> Info : JTAG tap: avr32.cpu tap/device found: 0x21e8203f (mfg: 0x01f, part: 0x1e82, ver: 0x2)
> Warn : JTAG tap: avr32.cpu       UNEXPECTED: 0x21e8203f (mfg: 0x01f, part: 0x1e82, ver: 0x2)
