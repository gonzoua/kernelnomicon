---
title: FreeBSD/MIPS & clang/LLVM
date: '2010-01-18T21:49:00'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Now I know a little bit more about clang. I can't say it's quite useful for MIPS yet, but it's nice to see some progress in this field.  My findings might be inaccurate and any corrections are welcome.

- No proper cross-compilation so far. Clang team plans to do it "right way" but so far it's just an item on their ToDo list: [Universal driver](http://clang.llvm.org/UniversalDriver.html). There are command-line options like -ccc-host-triple  to get target assembler code, but names for assembler/linker are hardcoded to as/ld, which puts some restriction on building target toolchain 

UPD: -ccc-host-triple does the trick OK

- No proper march/mabi handlers for MIPS platform. Only ARM and x86 are supported. 

I managed to build hello world application in semi-automatic mode on RouterStation booted over NFS:

> [root@ /llvm/llvm]# clang -v -fPIC  -Wa,-mabi=32,-EB,-KPIC hello.c
> clang version 1.1 (trunk)
> Target: mips-unknown-freebsd9.0
> Thread model: posix
> "/opt/bin/clang" -cc1 -triple mips-unknown-freebsd9.0 -S -disable-free -main-file-name hello.c -pic-level 2 -mdisable-fp-elim -v -resource-dir /opt/lib/clang/1.1 -fmessage-length 0 -fgnu-runtime -fdiagnostics-show-option -fcolor-diagnostics -o /tmp/cc-tuAxTl.s -x c hello.c
> clang -cc1 version 1.1 based upon llvm 2.7svn hosted on mips-unknown-freebsd9.0
> #include "..." search starts here:
> #include <...> search starts here:
> /opt/lib/clang/1.1/include
> /usr/local/include
> /usr/include
> End of search list.
> "/usr/bin/as" -mabi=32 -EB -KPIC -o /tmp/cc-LZzHnl.o /tmp/cc-tuAxTl.s
> "/usr/bin/ld" --eh-frame-hdr -dynamic-linker /libexec/ld-elf.so.1 -o a.out /usr/lib/crt1.o /usr/lib/crti.o /usr/lib/crtbegin.o /tmp/cc-LZzHnl.o -lgcc --as-needed -lgcc_s --no-as-needed -lc -lgcc --as-needed -lgcc_s --no-as-needed /usr/lib/crtend.o /usr/lib/crtn.o
> [root@ /llvm/llvm]# ./a.out
> Hello world!
> [root@ /llvm/llvm]# uname -a
> FreeBSD  9.0-CURRENT FreeBSD 9.0-CURRENT #28: Fri Jan 15 16:50:23 PST 2010     gonzo@figaro.bluezbox.com:/src/FreeBSD/obj/head-mips/mips/src/FreeBSD/head/sys/AR71XX  mips
> [root@ /llvm/llvm]#

So it seems to be nice starting point for some minor compiler hacking :)
