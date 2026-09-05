---
title: bsdfb platform plugin merged to Qt dev branch
date: '2016-06-13T17:28:22'
draft: false
author: Oleksandr Tymoshenko
tags:
- Qt
- freebsd
categories:
- FreeBSD
---

Few weeks back Ralf Nolden, who is *BSD champion in Qt community, urged me to clean-up and submit my Qt5-related projects to upstream and [scfb platform plugin](https://github.com/gonzoua/qt-platform-scfb) was picked as a test dummy. It took  [12 iterations](https://codereview.qt-project.org/#/c/159316/) to get things right, along the way plugin was renamed to bsdfb, but eventually patch has been [merged](https://github.com/qtproject/qtbase/commit/1542d8881fc5ccbc5918cd4acbe4091ebbd24508).

Next two candidates are [bsdkeyboard and bsdsysmouse input plugins](https://github.com/gonzoua/qt5-bsd-input).
