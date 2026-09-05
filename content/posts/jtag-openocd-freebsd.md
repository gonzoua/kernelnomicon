---
title: JTAG, OpenOCD & FreeBSD
date: '2009-04-19T18:51:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- EJTAG
- Flyswatter
- FreeBSD
- MIPS
- openocd
---

As it was [mentioned earlier](http://bsddev.blogspot.com/2009/02/wip-ubiquitys-router-station.html) now I have new cool toy to play with. Flyswatter JTAG with MIPS14 adapter. Though Tin Can Tools kindly warned me that OpenOCD did not support EJTAG/MIPS I decided to order it and it turned to be a good deal. Why? Because there is EJTAG/MIPS support for OpenOCD as of 0.1.0. Moreover it works really nice with FreeBSD port of libftdi. So all I had to do was to make [devel/openocd](http://people.freebsd.org/~gonzo/ports/openocd.tar) port and tweak some configs. And that's it. Stock gdb for MIPS is not ready yet, but one can attach, examine registers, single step using telnet interface to daemon: 

> [gonzo@figaro:][~/FreeBSD]
> % telnet localhost 4444
> Trying 127.0.0.1...
> Connected to localhost.
> Escape character is '^]'.
> Open On-Chip Debugger
> > halt
> target state: halted
> target halted due to debug-request, pc: 0x8023b974
> > reg
> (0) zero (/32): 0x00000000 (dirty: 0, valid: 1)
> (1) at (/32): 0x802c0000 (dirty: 0, valid: 1)
> (2) v0 (/32): 0x00000001 (dirty: 0, valid: 1)
> (3) v1 (/32): 0x00000000 (dirty: 0, valid: 1)
> (4) a0 (/32): 0x00000000 (dirty: 0, valid: 1)
> (5) a1 (/32): 0x00000000 (dirty: 0, valid: 1)
> (6) a2 (/32): 0x80280b28 (dirty: 0, valid: 1)
> (7) a3 (/32): 0x00000602 (dirty: 0, valid: 1)
> (8) t0 (/32): 0x802c10b0 (dirty: 0, valid: 1)
> (9) t1 (/32): 0x00000000 (dirty: 0, valid: 1)
> (10) t2 (/32): 0x00000000 (dirty: 0, valid: 1)
> (11) t3 (/32): 0x00000000 (dirty: 0, valid: 1)
> (12) t4 (/32): 0x00000000 (dirty: 0, valid: 1)
> (13) t5 (/32): 0x00000001 (dirty: 0, valid: 1)
> (14) t6 (/32): 0x00000000 (dirty: 0, valid: 1)
> (15) t7 (/32): 0x00000000 (dirty: 0, valid: 1)
> (16) s0 (/32): 0xc082abe0 (dirty: 0, valid: 1)
> (17) s1 (/32): 0x801312a8 (dirty: 0, valid: 1)
> (18) s2 (/32): 0x00000000 (dirty: 0, valid: 1)
> (19) s3 (/32): 0xc0828b20 (dirty: 0, valid: 1)
> (20) s4 (/32): 0xc0793ea0 (dirty: 0, valid: 1)
> (21) s5 (/32): 0x00000000 (dirty: 0, valid: 1)
> (22) s6 (/32): 0x00000000 (dirty: 0, valid: 1)
> (23) s7 (/32): 0x00000000 (dirty: 0, valid: 1)
> (24) t8 (/32): 0x02887fa0 (dirty: 0, valid: 1)
> (25) t9 (/32): 0x00000002 (dirty: 0, valid: 1)
> (26) k0 (/32): 0x8024e3a0 (dirty: 0, valid: 1)
> (27) k1 (/32): 0x00000000 (dirty: 0, valid: 1)
> (28) gp (/32): 0x00000000 (dirty: 0, valid: 1)
> (29) sp (/32): 0xc0793e30 (dirty: 0, valid: 1)
> (30) fp (/32): 0x00000000 (dirty: 0, valid: 1)
> (31) ra (/32): 0x8023b964 (dirty: 0, valid: 1)
> (32) status (/32): 0x0000ff01 (dirty: 0, valid: 1)
> (33) lo (/32): 0x0000001c (dirty: 0, valid: 1)
> (34) hi (/32): 0x00000020 (dirty: 0, valid: 1)
> (35) badvaddr (/32): 0xc0797dc8 (dirty: 0, valid: 1)
> (36) cause (/32): 0x40008000 (dirty: 0, valid: 1)
> (37) pc (/32): 0x8023b974 (dirty: 0, valid: 1)
> >
