---
title: MFC tracking web-tool
date: '2016-10-16T19:50:03'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Next week I plan to do bunch of MFCs for ARM and evdev stuff and to make it less of an ordeal I made this tool to track what's merged and what's not: [mfc.kernelnomicon.org](http://mfc.kernelnomicon.org). It provides basic functionality I thought I would need for this process:

- Navigator for HEAD commits with visual representation of MFC state: "no mfc scheduled", "ready for mfc", "waiting for 'mfc after' date", "merged"
- Basic filtering: by author name, by two states: "waiting", and "ready"
- "MFC basket" - manage set of commits I would like to merge back in one go
- Generate svn command and change log for selected "MFC basket"

Stack used: Django + Bootstrap3 + jQuery

GitHub project: [mfctracker](https://github.com/gonzoua/mfctracker)
