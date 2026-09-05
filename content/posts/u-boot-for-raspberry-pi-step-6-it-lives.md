---
title: 'U-Boot for Raspberry Pi, step 6: It lives!'
date: '2012-06-15T20:15:27'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

And finally it works! 

```
U-Boot 2012.04.01-00306-gecc6e3d-dirty (Jun 15 2012 - 19:05:09)

DRAM:  128 MiB
WARNING: Caches not enabled
Using default environment

In:    serial
Out:   serial
Err:   serial
Net:   No ethernet found.
Raspberry-Pi # usb start
(Re)start USB...
USB:   Core Release: 2.80a
scanning bus for devices... 3 USB Device(s) found
       scanning bus for ethernet devices... 1 Ethernet Device(s) found
Raspberry-Pi # dhcp
Waiting for Ethernet connection... done.
BOOTP broadcast 1
*** Unhandled DHCP Option in OFFER/ACK: 28
*** Unhandled DHCP Option in OFFER/ACK: 28
DHCP client bound to address 192.168.10.21
Using sms0 device
TFTP from server 192.168.10.1; our IP address is 192.168.10.21
Filename 'kernel.RPI'.
Load address: 0x700000
Loading: #################################################################
         #################################################################
         #################################################################
         #################################################################
         ######################################################
done
Bytes transferred = 4597674 (4627aa hex)
Raspberry-Pi # ping 192.168.10.1
Waiting for Ethernet connection... done.
Using sms0 device
host 192.168.10.1 is alive
Raspberry-Pi #
```
