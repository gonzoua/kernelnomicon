---
title: HDMI progress and 4DCAPE-43 support for Beaglebone Black
date: '2015-02-28T19:54:28'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

HDMI support for Beaglebone Black is stable now and supports reading EDID, you can get the path [here](https://people.freebsd.org/~gonzo/arm/patches/bbb-hdmi-20150128.diff). Before committing it I'd like to make interoperability between HDMI framer and FB/LCD drivers as generic as possible and for this I need at least one more system with working HDMI to find common patterns. For this purpose I picked up i.MX6-based [Hummingboard](http://www.solid-run.com/products/hummingboard/) and now try to get video output working on it. There is some minor progress but it seems before getting to HDMI/IPU I need to do some work on clock management part of the system. So it's going to be some time before I see first pixels on my monitor. 

I also got [4DCAPE-43](http://www.4dsystems.com.au/product/4DCAPE_43/), neat Beaglebone Black LCD cape by 4D Systems. Patch ([download here](https://people.freebsd.org/~gonzo/arm/patches/bbb-4dcape-43.diff)) for it is pretty minimal: VT support in kernel config, panel info and pins configuration in dts, and one improvement in GPIO driver (setting default values for OUT GPIO pins). None of the other features except reset button work yet but getting GPIO keyboard working would be an interesting project by itself. 

Here is the picture of cape in action:

[![IMG_0685](/uploads/2015/02/IMG_0685-1024x768.jpg)](/uploads/2015/02/IMG_0685.jpg)
