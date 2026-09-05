---
title: VCHI driver, part 2
date: '2013-01-08T15:33:02'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Some time ago I [announced](http://kernelnomicon.org/?p=185) port of VCHI driver to FreeBSD. Since then it was re-licensed as BSD/GPL and I had high hopes for bringing it into the tree as a part of sys/contrib. This weekend I finally got around to it but  turned out things had changed for worse. VCHI driver used to have this neat OS-abstraction wrapper, so overall porting process was quite simple: implement synch primitives, physical pages management, driver-specific initialization and you're done. But... The layer was lost with driver update in October. The reason for it - [OS compatibility shims are banned from Linux mainline kernel](https://github.com/raspberrypi/firmware/issues/130). 

So now I face two options: create complete port of VCHI driver by replacing linux-specific parts with freebsd-specific code.  Or create Linux compatibility shims and try to keep sources as close as possible to upstream. I'm going to try latter approach in order to minimize maintenance work. If it doesn't work out - I'll fall back to the former. But with any of these approaches code difference with upstream will be too significant to go to contrib tree and most likely driver will be distributed as a port.
