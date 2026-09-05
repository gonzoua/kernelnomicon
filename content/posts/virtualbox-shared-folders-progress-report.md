---
title: 'VirtualBox Shared Folders: progress report'
date: '2016-09-12T15:59:05'
draft: false
author: Oleksandr Tymoshenko
tags:
- freebsd
- shared folders
- vbox
categories:
- FreeBSD
---

I spent Labor Day weekend laboring on VBox shared folders support for FreeBSD. It's been some time since I worked on it last time so I had to refresh my memory first. Things have moved on since then - VBox in ports was updated to version 5, but fortunately Li-Wen synced up [freebsd-vboxfs](https://github.com/lwhsu/freebsd-vboxfs) repo to the latest version. After three days of laid-back hacking I am glad to announce that following VOPs are kind of implemented (in no particular order): lookup, access, readdir, read, getattr, readlink, remove, rmdir, symlink, close, create, open, write. "Kind of implemented" means that I was able to mount directory, traverse it, read file, calculate md5 sums and compare with host's md5sum, create/remove directories, unzip zip file, etc but I doubt it would survive stress-test. Locking is all wrong at the moment and read/write VOPs allocate buffers for every operation.

I hit a roadblock with rename VOP: it involves some non-trivial locking logic and also there is a problem with cached paths. VBox hypervisor operates on full paths so we cache them in vboxfs nodes, but if one of parent directories is renamed, all cached names should be modified accordingly. I am going to tackle these two problems once I have long enough stretch of time time sit and concentrate on task.
