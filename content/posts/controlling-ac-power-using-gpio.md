---
title: Controlling AC power using GPIO
date: '2015-12-24T12:50:16'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Being able to power cycle ARM boards remotely (without spending a lot of $$$) was on my wish list for way to long, so I finally got around to put something together. The obvious way to do this is power relay controlled by GPIO + remotely accessible GPIO port. For the former I picked up [this relay by Digital Loggers](http://www.amazon.com/gp/product/B00WV7GMA2). It was four ports, only two of them are connected at a time, you can switch selected pair by setting control port level. For a controller part I picked up RIoTBoard but actually any ARM board with  sshd running on it and user-accessible GPIO pin would do. 

On riotboard there are several GPIO pins available on J13 expansion port. You can find values in [board's user manual](http://riotboard.org/download/RIoTboard-User-Manual-V2.1.pdf). Just in case you don't know (and it's not in manual) pin 1 on the port marked with white triangle, if you look at port so that pin 1 is in top-left corner, next to it on the right is pin 2, and right under it is pin 3. So it goes like:
[ 1 2 ]
[ 3 4 ]
[ 5 6 ]
... and so on ...

I used GPIO4_16 (pin 5). You need to connect GND and GPIO pin to relay's control port. Polarity does not matter in this case. For port control you can use gpioctl utility. But first we need to identify this pin on FreeBSD's side. For each GPIO bank FreeBSD has /dev/gpiocX device node, where X starts from zero. But it's not uncommon for SoC vendors to start numbering GPIO banks from 1. riotboard's vendor is one of them so GPIO4 is actually controlled through /dev/gpioc3. By default pin 16 is configured as input, so you need to change it to out:

```
# gpioctl -f /dev/gpioc3 -c 16 OUT
```

and then you can start giving orders to relay:

```
# gpioctl -f /dev/gpioc3 16 0
# gpioctl -f /dev/gpioc3 16 1
# gpioctl -f /dev/gpioc3 -t 16
```

Demo video:
https://youtu.be/jsWATafJZ_I
