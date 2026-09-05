---
title: More on OpenOCD and MIPS/EJTAG
date: '2009-04-27T22:36:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- EJTAG
- MIPS
- openocd
---

Well, I was overly optimistic. OpenOCD can halt CPU, reset, examine registers and perform software breakpoints. Trying to figure out what's wrong with EJTAG support I skimmed the code and the state of it is, well, poor. Current implementation assumes that target is of the same endianness as host. That's why number of HW breakpoints/watchpoints was detected wrong. But even fixing (endianness and breakpoints) and implementing(watchpoints) in hack'n'slash mode didn't help. I still can't get bp/wp to work. But I'll definitely try. May be next weekend.
