---
title: 'OS X: Flyswatter, JTAG & RS232'
date: '2009-11-23T19:33:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- Flyswatter
- JTAG
- os x
---

Since I lent my RS232/USB adapter to a friend it was nice opportunity to experiment with Flyswatter on-board RS232 port. OS X does not support FTDI devices out of the box, but it's not a problem for OpenOCD, which uses generic interface to communicate JTAG board. Things get a little bit more complicated when you're trying to get on-board RS232 port working.

I started with installing stock drivers from [FTDI site](http://www.ftdichip.com/Drivers/VCP.htm). Current version supports Snow Leopard. With these drivers installed system started to detect two COM ports but JTAG part stopped to work as OpenOCD failed to claim USB device. 

Long story short: you'll need to edit kext content to prevent first usb device from being recognized as a virtual COM port. Nothing fancy, simple text editor would do the trick for all you need to edit is plain XML file. Find line **<key>FT2232C_A</key>** and delete it along with following **<dict>** element. Reload kext using kextunload/kextload and voila - JTAG works, "cu -s 115200 -l cu.usbserial-FS000000B" works.
