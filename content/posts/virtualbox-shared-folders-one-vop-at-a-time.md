---
title: 'VirtualBox Shared Folders: One VOP at a Time'
date: '2016-06-12T18:31:32'
draft: false
author: Oleksandr Tymoshenko
tags:
- freebsd
- shared folder
- virtualbox
categories:
- FreeBSD
---

Two months ago I tried to setup dev environment using FreeBSD Vagrant box just to find out that FreeBSD does not support VirtualBox shared folders. After some googling I found Li-Wen Hsu's github [repository](https://github.com/lwhsu/freebsd-vboxfs) with some work in this area. Li-Wen and Will Andrews has already done major chunk of work: patches to VirtualBox build system, skeleton VFS driver, API to talk to hypervisor but hit a block with some implementation details in VirtualBox's virtual-memory compatibility layer. Will provided [very comprehensive analysis](https://github.com/lwhsu/freebsd-vboxfs/blob/master/vboxvfs/vboxvfs_prov.c#L131) of the problem. 

Li-Wen was occupied with some other projects so he gave me his OK to work on shared folder support on my own. Will's suggestion was easy to implement - lock only userland memory, like Solaris driver does. VFS part was more complicated though: fs nodes, vnode, their lifecycle and locking is too hairy for drive-by hacking. I used tmpfs as a reference to learn some VFS magic, but a lot of things are still obscure. Nevertheless after few weeks of tinkering first milestone has been achieved: I can mount/unmount shared VirtualBox folder and navigate mounted filesystem without immediate kernel panic. Next goal (if time permits): stable and non-leaking read-only filesystem.
