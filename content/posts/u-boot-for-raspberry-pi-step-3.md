---
title: U-Boot for Raspberry Pi, step 3
date: '2012-06-03T14:06:30'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Finally I received my female/male cables for inter-board connects (my soldering skill is close to zero) and got FTDI-based TTL/USB converter work. I had more luck with it then with my old trusty TTL/RS232 converter. No garbage in linux output, serial input works for u-boot. 

You can find content of boot partition of SD-card I use [here](http://people.freebsd.org/~gonzo/r-pi/u-boot-pi.tar.gz).

Fully functional serial port means that boot-over-serial should work and I do not need to move SD-card between R-Pi and card reader to get new version of u-boot running. So based on [this post](http://blog.mezeske.com/?p=483) I managed to automate compile/run/fix cycle to some extent. Not C-Kermit expert here, so the script is lame but it does its work:

```
#!/home/gonzo/bin/wermit

# Serial port setup.  These settings will likely need to be
# changed to match the configuration of your workstation
# and the ARM board you're working with.
set line /dev/ttyUSB3
set speed 115200
set serial 8n1

# General C-Kermit settings.  These probably don't need to change.
set flow-control none
set file type bin
set carrier-watch off
set prefixing all
set modem none

# load binary
input 90 "Raspberry-Pi # "
lineout "loadb 0x00000000"
# This should be the absolute path to your kernel uImage file.
send /home/gonzo/1/kernel.img

# This command drops you into a console where you can interact
# with the kernel.
connect
```

So now when I'm working on USB support for R-Pi the development cycle looks like this:

- Hack-hack-hack, compile, generate kernel.img, place kernel.img to ~gonzo/1/
- power-cycle board, run script
- Press Enter to trigger file upload and wait for this:

```
## Total Size      = 0x0002b6d0 = 177872 Bytes
## Start Addr      = 0x00000000
```
- Issue **go 0** command
- Issue **usb start** command, wait for results and start over
