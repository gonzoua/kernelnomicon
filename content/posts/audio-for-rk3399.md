---
title: Audio for RK3399
date: '2019-11-30T19:46:08'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

Last two weeks I've been working on audio support for Firefly-RK3399. Full support requires a number of things that are not quite there or not available in the mainline FreeBSD kernel. The main low-level hardware functionality consists of two parts: I2S block in the SoC and RT5640 audiocodec that converts digital audio to an analog signal. They talk to each other using the I2S protocol. A little bit higher is FDT virtual "devices" called simple-audio-card. This part is responsible for coordinating the setup of both hardware components: make sure they agree on a number of channels, a number of bits per sample and clock specifics of the I2S protocol. There is no code for it in the FreeBSD kernel, so I had to just hardcode these things in both hardware drivers.

The other obviously missing part was I2S clocks in the CRU unit drivers. This was easily fixable by just consulting with RK3399 TRM. Still, having added the clocks support I couldn't get the signal on the physical pin. Thanks to manu@ who pointed out that some stuff might be missing in io-domain and power regulators area it was resolved too after setting bit 1 of GRF_IO_VSEL register

With all these bits in place, I was able to get sound out of headphones, but it was distorted. My first instinct was to blame mismatch of clocks/formats between I2S and codec, so I spent two very frustrating days experimenting with setting polarities and data formats just to find out that there is GPIO that controls headphone output on Firefly-RK3399. This is Firefly-specific bit and only referenced in the Firefly fork of the Rockchip fork of the Linux kernel. The way it's implemented loud noises still can pass through the filter to headphones, so at max volume, you can here laud parts as a series of pops and grunts. By manually configuring GPIO4_C5 (gpioc4/pin21) I finally was able to get clear sound out of the Firefly.

On the bright side now I know how to convert an I2S stream captured by a Saleae logic analyzer to wav file: [https://github.com/roel0/PCM2Wav-py](https://github.com/roel0/PCM2Wav-py)

The next step is to check how much work would it take to implement the simple-audio-card part. The (very hacky) WIP is at [https://github.com/gonzoua/freebsd/commits/rk3399_audio](https://github.com/gonzoua/freebsd/commits/rk3399_audio)

![](/uploads/2019/11/scope-300x225.jpg)

![](/uploads/2019/11/scope2-300x225.jpg)
