---
title: 'Netbooting ARM/MIPS devices: server setup'
date: '2013-02-14T18:50:42'
draft: false
author: Oleksandr Tymoshenko
tags:
- arm
- netboot
categories:
- Uncategorized
---

I was asked to share details about my root-over-NFS setup so here they are. I decided to split how-to in two posts: server/kernel part and u-boot part. 

Usual components in the setup are:

- DHCP server
- TFTP server
- NFS server
- NAT (optional)

## DHCP server

I use **net/isc-dhcp42-server** as a server. Sample dhcpd.conf:

```
option root-opts code 130 = string; # NFS / mount options
log-facility local7;

subnet 192.168.10.0 netmask 255.255.255.0 {
        server-name "cinderella.bluezbox.com";
        server-identifier 192.168.10.1;
        option subnet-mask 255.255.255.0;
        option broadcast-address 192.168.10.255;
        option domain-name-servers 8.8.8.8;
        option domain-name "bluezbox.com";
        next-server 192.168.10.1;
        option routers 192.168.10.1;
}

group {
        host pandaboard {
                hardware ethernet 0E:60:33:B1:46:01;
                fixed-address 192.168.10.90;
                filename "kernel.PANDA.bin";
                option root-path "/src/FreeBSD/nfs/armv6";
                option root-opts "nolockd";
        }

        host rpi {
                hardware ethernet b8:27:eb:f6:08:83;
                fixed-address 192.168.10.91;
                filename "ubldr";
                option root-path "/src/FreeBSD/nfs/rpi";
                option root-opts "nolockd";
        }
}
```

Config is pretty self-explanatory. I use google's **8.8.8.8** nameserver but you can change it to your very own DNS server. Difference between various **filename "..."** will be explained later.

dhcpd should be enabled in [rc.conf(5)](http://www.freebsd.org/cgi/man.cgi?query=rc.conf&sektion=5)

```
dhcpd_enable="YES"
```

## TFTP server

TFTP server provides access to all files described in **filename "..."** options so it's better to keep them together. By default it's **/tftpboot** directory but I have whole drive dedicated to FreeBSD development environment and mounted under /src/FreeBSD mountpoint. So I keep everything there and my TFTP server root is **/src/FreeBSD/tftpboot**. TFTP server is standard FreeBSD's one and config line in [inetd.conf(8)](http://www.freebsd.org/cgi/man.cgi?query=inetd.conf&sektion=8) looks like:

```
tftp    dgram   udp     wait    root    /usr/libexec/tftpd      tftpd -l -s /src/FreeBSD/tftpboot
```

  

inted should be enabled in [rc.conf(5)](http://www.freebsd.org/cgi/man.cgi?query=rc.conf&sektion=5)

```
inetd_enable="YES"
```

## NFS Server

NFS server is not just one daemon but several services combined. So [rc.conf(5)](http://www.freebsd.org/cgi/man.cgi?query=rc.conf&sektion=5) part of config looks like this:

```
rpcbind_enable="YES"
rpc_statd_enable="YES"
rpc_lockd_enable="YES"
nfs_server_enable="YES"
mountd_enable="YES"
```

Filesystems that are exported via NFS listed in [exports(5)](http://www.freebsd.org/cgi/man.cgi?query=exports&sektion=5). Mine contains following:

```
# Mind mount points obj and nfs are different
/usr/ports -maproot=0 -network 192.168.10.0/16
/src/FreeBSD/head /src/FreeBSD/nfs/rpi /src/FreeBSD/nfs/armv6 /src/FreeBSD/nfs/am335x -maproot=0 -network 192.168.10.0/16
```

Note that you can join several directories into one line only if they belong to the same mount point.

## NAT

If you're planning on building ports on the device - you'll need internet access on it. All my devices  are restricted to one LAN with laptop acting as a gateway. I use [pf(4)](http://www.freebsd.org/cgi/man.cgi?query=pf&sektion=4) for NATing. Config:
[/etc/pf.conf(5)](http://www.freebsd.org/cgi/man.cgi?query=pf.conf&sektion=5)

```
ext_if=em0
rede="{192.168.0.0/16}"

nat on $ext_if from $rede to any -> ($ext_if)
```

 

and [rc.conf(5)](http://www.freebsd.org/cgi/man.cgi?query=rc.conf&sektion=5)

```
gateway_enable="YES"

pf_enable="YES"
pf_rules="/etc/pf.conf"
pf_flags=""
```

## Kernel config

FreeBSD kernel should be properly configured in order to be suitable for mounting root over NFS:

```
options         NFSCL
options         NFSCLIENT # NFS v3
options         NFS_ROOT
options         BOOTP_NFSROOT
options         BOOTP_COMPAT
options         BOOTP
options         BOOTP_NFSV3
options         BOOTP_WIRED_TO=ue0
```

BOOTP_WIRED_TO value is SoC-specific. If you do not have full control over your DHCP server (e.g. it's cable modem) and can't specify root-path/root-opts you still can hardcode root location by removing BOOTP_NFSROOT and adding

```
options         ROOTDEVNAME=\"nfs:192.168.10:/src/FreeBSD/nfs/rpi\"
```

## Installation

Depending on your boot sequence installation consists of one or two steps. 

Normal system installation, e.g.:

```
sudo -E make TARGET_ARCH=armv6 DESTDIR=/src/FreeBSD/nfs/rpi -DDB_FROM_SRC installworld
sudo -E make TARGET_ARCH=armv6 DESTDIR=/src/FreeBSD/nfs/rpi -DDB_FROM_SRC distribution
```

And installing kernel copying kernel to tftpboot directory:

```
sudo -E make TARGET_ARCH=armv6 DESTDIR=/src/FreeBSD/nfs/rpi -DDB_FROM_SRC installkernel
cp /src/FreeBSD/nfs/rpi/boot/kernel/kernel /src/FreeBSD/tftpboot/kernel.RPI 
```

In some cases you'd want to use kernel.bin instead of kernel (more on it in next post) so second step would look like

```
sudo -E make TARGET_ARCH=armv6 DESTDIR=/src/FreeBSD/nfs/rpi -DDB_FROM_SRC KERNEL_EXTRA_INSTALL=kernel.bin installkernel
cp /src/FreeBSD/nfs/rpi/boot/kernel/kernel.bin /src/FreeBSD/tftpboot/kernel.RPI.bin
```

And if you're going to use ubldr, there is no need to copy installed kernel anywhere.

More details on different types of binaries and boot process in the next post
