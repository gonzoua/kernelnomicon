---
title: i.MX6 IPU and HDMI drivers
date: '2015-12-09T15:10:39'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Back from vacation and back to work. Once I got RIoTboard up and running next natural step was to wrap up some i.MX6 project I had in "almost finished" state for months. So now they're in "going through review" state: drivers for [HDMI framer](https://reviews.freebsd.org/D4174) and [IPU](https://reviews.freebsd.org/D4168). They add basic 1024x768 console for iMX6 board. Video mode management requires more sophisticated timers framework, that is being work on as a part of Jetson TK1 port.
