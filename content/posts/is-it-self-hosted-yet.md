---
title: Is it self-hosted yet?
date: '2009-07-08T17:18:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Good news everyone! Last two days I've been testing FreeBSD/mips by running buildworld on netbooted [RouterStation](http://wiki.freebsd.org/FreeBSD/mips/UBNT-RouterStation) with root mounted over NFS. So far so good, it successfully completed twice. 

A couple of weeks ago arch-dependent part of libthr (both kernel and userland) was implemented and it seems to work. "Seems to work" means that it passes thr1 and thr2 tests from stress2 and python's test_thread[ing].py. And yes, python and perl build fine on the same board from the ports do work. As bash et al :) Though perl should be built without Perl malloc but it's on my ToDo list.
