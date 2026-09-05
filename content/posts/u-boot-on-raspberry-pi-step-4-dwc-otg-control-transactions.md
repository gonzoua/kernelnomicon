---
title: 'U-Boot on Raspberry Pi, step 4: DWC OTG, Control transactions'
date: '2012-06-12T00:20:11'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

After rather busy weekend I finally got control transaction support for DWC OTG controller working on Raspberry Pi. U-Boot DWC OTG driver is cut-down version of Linux driver that works in polling mode. Stability is not very good at the moment though. And make sure to use latest firmware files. It does matter. 

Next step - bulk transactions. 

Repo: [https://github.com/gonzoua/u-boot-pi](https://github.com/gonzoua/u-boot-pi)

```
Raspberry-Pi # usb info
1: Hub,  USB Revision 1.10
 - DWC OTG RootHub
 - Class: Hub
 - PacketSize: 8  Configurations: 1
 - Vendor: 0x0000  Product 0x0000 Version 0.0
   Configuration: 1
   - Interfaces: 1 Self Powered 0mA
     Interface: 0
     - Alternate Setting 0, Endpoints: 1
     - Class Hub
     - Endpoint 1 In Interrupt MaxPacket 8 Interval 255ms

2: Hub,  USB Revision 2.0
 - Class: Hub
 - PacketSize: 64  Configurations: 1
 - Vendor: 0x0424  Product 0x9512 Version 2.0
   Configuration: 1
   - Interfaces: 1 Self Powered Remote Wakeup 2mA
     Interface: 0
     - Alternate Setting 0, Endpoints: 1
     - Class Hub
     - Endpoint 1 In Interrupt MaxPacket 1 Interval 255ms

3: Vendor specific,  USB Revision 2.0
 - Class: Vendor specific
 - PacketSize: 64  Configurations: 1
 - Vendor: 0x0424  Product 0xec00 Version 2.0
   Configuration: 1
   - Interfaces: 1 Self Powered Remote Wakeup 2mA
     Interface: 0
     - Alternate Setting 0, Endpoints: 3
     - Class Vendor specific
     - Endpoint 1 In Bulk MaxPacket 64
     - Endpoint 2 Out Bulk MaxPacket 64
     - Endpoint 3 In Interrupt MaxPacket 16 Interval 1ms
```
