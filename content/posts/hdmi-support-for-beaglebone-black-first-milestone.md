---
title: 'HDMI support for Beaglebone Black: first milestone'
date: '2015-01-18T18:56:06'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Today for the first time I've got stable and correctly positioned output on HDMI monitor connected to BeagleBone Black. It involved fixing bug in AM335x LCDC controller, fixing bug in I2C controller, and a lot of experiments with register-pushing. Code requires major clean-up and is not ready for the tree yet. I'll post patch when it's in readable form. 

[![IMG_0650](/uploads/2015/01/IMG_0650-1024x768.jpg)](/uploads/2015/01/IMG_0650.jpg)
