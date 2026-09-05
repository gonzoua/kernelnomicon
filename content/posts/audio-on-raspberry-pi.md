---
title: Audio on Raspberry Pi
date: '2015-01-09T23:25:08'
draft: false
author: Oleksandr Tymoshenko
tags:
- audio
- freebsd
- vchiq
categories:
- ARM
- FreeBSD
---

With stable VCHIQ driver next obvious target was to add VCHIQ-based audio support. So let me introduce to you: vchiq_audio, first take. It's part of vchiq-freebsd repo so if you use Crochet to build SD card image just enable `option VideoCore` in config file and module will be automatically included. 

From shell run `kldload vchiq_audio` and you're good to do. I believe that audio output is picked up automatically by VideoCore so if you have HDMI connected it's probably going to be HDMI. I do not have device to confirm this. Adding knob to control audio output (auto, headphones, HDMI) is on my ToDo list. 

Quality is not ideal though. From quick tests it seems to work fine on system with rootfs on NFS but there are audio drops on SD-based system while playing mp3 over NFS. I'm going to debug and stresstest it more thoroughly next week.

Short instruction on how to install mpg321 package on RPi:

```
env PACKAGESITE=http://chips.ysv.freebsd.org/packages/11armv6-11armv6/ SIGNATURE_TYPE=none pkg bootstrap

mkdir -p /usr/local/etc/pkg/repos
cd /usr/local/etc/pkg/repos
echo 'FreeBSD: { enabled: no }' > FreeBSD.conf

cat > chips.ysv.conf <<__EOF__
chips.ysv: {
  url: "http://chips.ysv.freebsd.org/packages/freebsd:11:armv6:32:el:eabi:softfp",
  mirror_type: "http",
  signature_type: "none",
  enabled: yes
}
__EOF__

pkg install mpg321
```
