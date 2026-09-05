---
title: Netbooting FreeBSD with PXE/UEFI
date: '2016-09-29T16:11:10'
draft: false
author: Oleksandr Tymoshenko
tags:
- freebsd
- pxe
- uefi
categories:
- Uncategorized
---

Dear Future Me,

I guess you came here googling for "FreeBSD PXE UEFI" trying to find out how to netboot your x86 dev box. Or arm64 box. Who knows what you're hacking on in the future. To do that you need follow these simple steps:

- Put loader.efi to tftpboot dir
- Configure dhcpd along these lines:

```
host amd64 {
        hardware ethernet  b8:ae:ed:77:88:99;
        filename "loader.efi";
        option root-path "/src/FreeBSD/tftproot/amd64";
        fixed-address 192.168.10.102;
        option routers 192.168.10.1;
}
```
- Make sure root-path is in /etc/exports.
- If you use MINIMAL-derived config add your NIC driver to /boot/loader.conf:

```
if_re_load="YES"
```

That's pretty much it.

Take care
