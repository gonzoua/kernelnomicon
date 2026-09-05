---
title: wpa_supplicant.conf "manager" for coffee shops
date: '2020-05-12T22:59:46'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

![https://github.com/gonzoua/chaifi](/uploads/2020/05/screenshot-1024x576.png)

Whenever I sit down to work at the coffee shop I've never been before my preparation routine looks more or less like this: `ifconfig wlan0 list scan`, copy SSID, `vim /etc/wpa_supplicant.conf`, `service netif restart`. The WM I use, i3, does not have fancy network managers that nicely offer you to join available WiFi networks. It's mildly annoying but not annoying enough to actually make me open a browser and go looking for ways to automate this procedure. Implementing such a utility, on the other hand, sounds like a nice weekend project.

I wanted to try out the Go language for a long time but didn't have a good idea for a project and this WiFi manager tool looked like a good fit to learn how stuff is done there because it required: starting a child process and parsing its output, file I/O, work with strings, TUI elements.

The data management bits were easy, the standard library provides all the required functionality, and for TUI framework I picked up [termui](https://github.com/gizak/termui). Midway through implementation, I realized that the main use case of the framework is to present data, and interacting with the user was not its forte, but I decided to press on and just hack something together. It was just for fun after all. It worked out acceptable in the end.

Without further ado, let me introduce [Chai-Fi](https://github.com/gonzoua/chaifi), the minimalistic wpa_supplicant.conf "manager" for FreeBSD. To be used in coffee shops, guest networks, and other locations. It's got a bunch of stuff hardcoded, not very portable between different TERMs and probably won't look nice with non-Unicode fonts. But hey, it works on my laptop. Who knows, might work on yours
