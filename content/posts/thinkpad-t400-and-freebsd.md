---
title: Thinkpad T400 and FreeBSD
date: '2009-01-22T01:12:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
- t400
- thinkpad
---

Finally I got my hands on new and shiny Lenovo Thinkpad T400. What can I say? It's cool. My only complaint about T60 was its somewhat dim display (yes, I tried to save some money on this vital part and got punished). This time I ordered model with LED backlight and it's worth every dime spent :) 

My configuration also included Atheros wifi, built-in bluetooth, Intel GMA X4500 graphics (I'm not in games, really). Unfortunately 7.1 was able to run wifi. Newer HAL did the trick though and here I am: eating my own dog food - running -CURRENT on a workhorse. 

Intel graphics was kind of disappointment. I expected no problems with this integrated chipset but there were a lot. "Ghost" video output (or companion display) that was causing mplayer not to run in fullscreen mode (fixed with xrandr). With SWCursor option off text console is garbled when switching to it from X. mplayer is somewhat laggish in full screen mode :( I hope intel driver will be updated soon, meanwhile I'll refrain from watching movies (it should boost my productivity :))

UPD: It seems that Intel Video artifacts is due to unsymmetrical memory layout (I have 2G + 1G). Windows 7 is affected as well.
