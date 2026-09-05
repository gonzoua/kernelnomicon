---
title: 'Netbooting ARM/MIPS devices: kinds of kernel and u-boot'
date: '2013-02-15T16:33:58'
draft: false
author: Oleksandr Tymoshenko
tags:
- arm
- netboot
categories:
- FreeBSD
---

[U-Boot](http://www.denx.de/wiki/U-Boot) is a boot loader. Its task is to get kernel into memory and pass control to it. I will cover only parts of it related to netboot. 

## kernel or kernel.bin

But before we start loading something we need to know what to load. In [previous post](http://kernelnomicon.org/?p=306) I mentioned that there are kernel, kernel.bin, and ubldr files. Let's get into details. First of all: ubldr requires its own post. So there will be one more covering just ubldr. Now kernel and kernel.bin. 

kernel is ELF executable. It means that it's a self-contained file with all the information required to layout its bits in memory. e.g.: this data in file should be copied to address A, and N bytes at address B should be set to zero, code intry point is address X. All this auxiliary information is stored alongside to raw code and data. U-boot (or any other bootloader) reads it, lays out data/code accordingly and passes control to entry point. U-Boot's command for it is **bootelf**.

Now, **bootelf** or ELF support in general is not always available in boot loaders. In this case we load ELF on host machine. Technically it's called "convert to binary format" but essentially what objcopy utility does is it simulates loading of ELF file into memory and dumps memory region from the lowest address that belongs to loaded executable to the highest one into the kernel.bin file. No auxiliary information is saved - only raw code and data. Without this information it's users responsibility to point which address this memory dump should be loaded at and where to start execution. 

That's theory in a nutshell. Back to practice.

## U-Boot

Network initialization routine depends on the board you're working with. If the ethernet card connected to board over USB (like on Raspberry Pi or Pandaboard) you might need to initialize USB first:

```
U-Boot> usb start
(Re)start USB...
USB0:   Core Release: 2.80a
scanning bus 0 for devices... 3 USB Device(s) found
       scanning usb for storage devices... 0 Storage Device(s) found
       scanning usb for ethernet devices... 1 Ethernet Device(s) found
```

At this point you can either get network settings via DHCP or set them manually. 
Manual control over network is performed by setting U-Boot environment variables:

```
U-Boot> setenv ipaddr 192.168.10.21
U-Boot> setenv netmask 255.255.255.0
U-Boot> setenv gatewayip 192.168.10.1
```

DHCP also provides information about TFTP server and boot file, we can set them manually too:

```
U-Boot> setenv bootfile kernel
U-Boot> setenv serverip 192.168.10.1
```

And now load it 

```
U-Boot> tftpboot 0x8000
```

and boot

```
U-Boot> bootelf 0x8000
```

By default tftpboot and bootelf would use loadaddr env variable if it's set so you can combine last two commands to

```
U-Boot> setenv loadaddr 0x8000
U-Boot> tftpboot
U-Boot> bootelf
```

With DHCP everything above is combined into three commands:

```
U-Boot> setenv loadaddr 0x8000
U-Boot> dhcp
U-Boot> bootelf
```

If you're booting ELF loadaddr can be any valid address because bootelf will relocate kernel to proper location. Valid range for addresses depends on the board in use.

With kernel.bin though you have to specify specific value as a loadaddr. Usually it's KERNPHYSADDR option in kernel config file for ARM and KERNLOADADDR value for MIPS. U-Boot commands sequence would look like:

```
U-Boot> setenv bootfile kernel.bin
...
U-Boot> setenv loadaddr 0x00100000
U-Boot> dhcp
U-Boot> go 0x00100000
```

## uImage, ubldr

This is basic stuff I've been using for several years in my development environment. There are more options though: u-boot application images and **bootm** command and ubldr. Former is well-documented on Internet and about latter I'll post some information soon.
