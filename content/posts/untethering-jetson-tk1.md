---
title: Untethering Jetson-TK1
date: '2016-09-25T18:11:43'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

Normally I netboot all my ARM devices but in case of Jetson TK1 I thought it would be nice to go and try to make it "real computer" - running by itself, may be use it for some port builds. I added Samsung EVO to it but the plan was to use SSD for builds/source code, and to use either external SD card or eMMC as a root device. TK1 survived buildworld/buildkernel (I had to add swap though, clang is a memory monster) so it was time to populate root device and eMMC was picked as a target. There were some ms-basic-data partitions on eMMC but I didn't think much of it and happily typed dd if=/dev/zero of=/dev/mmcsd0 bs=128m. Well... Big mistake. Among those partitions was u-boot. And probably earlier stage boot loader as well. So I had to install ubuntu on one of unused machines and re-flash TK1. Luckily no permanent damage was done to the device. After this accident I added a little bit of planning into the process. Here is short summary:

Default eMMC partition looks like this:

```
Tegra124 (Jetson TK1) #  mmc part

Partition Map for MMC device 0  --   Partition Type: EFI

Part    Start LBA       End LBA         Name
        Attributes
        Type GUID
        Partition GUID
  1     0x00017000      0x01c16fff      "APP"
        attrs:  0x0001000000000001
        type:   ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
        guid:   7369c667-ff51-ec4a-29cd-baabf2fbe346
  2     0x01c17000      0x01c18fff      "DTB"
        attrs:  0x0002000000000001
        type:   ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
        guid:   f854c27c-e81b-8de7-765a-2e63339fc99a
  3     0x01c19000      0x01c38fff      "EFI"
        attrs:  0x0003000000000001
        type:   ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
        guid:   b70d3266-5831-5aa3-255d-051758e95ed4
  4     0x01c39000      0x01c3afff      "USP"
        attrs:  0x0004000000000001
        type:   ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
        guid:   c6cdb2ab-b49b-1154-0e82-7441213ddc87
  5     0x01c3b000      0x01c3cfff      "TP1"
        attrs:  0x0005000000000001
        type:   ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
        guid:   a13ee970-e141-67fc-3e01-7e97eadc6b96
  6     0x01c3d000      0x01c3efff      "TP2"
        attrs:  0x0006000000000001
        type:   ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
        guid:   2a5c388f-b0ec-fb3b-32af-3c54ec18db5c
  7     0x01c3f000      0x01c40fff      "TP3"
        attrs:  0x0007000000000001
        type:   ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
        guid:   43fe1a02-fafb-3aaa-fb29-d1e6053c7c94
  8     0x01c41000      0x01c41fff      "WB0"
        attrs:  0x0008000000000001
        type:   ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
        guid:   61bed875-f989-bb5c-a899-0f95b1ebf1b3
  9     0x01c42000      0x01d58fff      "UDA"
        attrs:  0x0009000000000001
        type:   ebd0a0a2-b9e5-4433-87c0-68b6b72699c7
        guid:   00f7ef05-a1e9-e53a-ca0b-cbd0484764bd
```

We need two things to make TK1 bootable: place ubldr on flash so u-boot can load it and create rootfs. The latter one is simple: APP partition is the one to be user as root. For the former either of TPx partitions can be used, TP stands for "Temporary Placeholder" and can be used for app-specific tasks. All three TPx are just 4Mb but ubldr is small enough to fit just fine. So let's place ubldr to TP1:

```
# newfs_msdos /dev/gpt/TP1
# mount_msdosfs /dev/gpt/TP1 /mnt
# cp ubldr.tk1 /mnt/
# umount /mnt
```

Then populate rootfs:

```
# newfs /dev/gpt/APP
# mount /dev/gpt/APP /mnt
```

Then build/install stuff: ... buildworld, buildkernel. installworld, distribution and installkernel with DESTDIR=/mnt ...

Create /etc/fstab so kernel would pick up correct rootfs location. I also use nullfs to mount /tmp to the directory on SSD drive, and added swap file on SSD.  

```
/dev/gpt/APP /       ufs     rw,noatime     0       0
/dev/ada0    /src    ufs     rw,noatime     0       0
md99         none    swap    sw,file=/src/swap,late      0       0
/src/tmp     /tmp    nullfs  rw             0       0
```

```
# umount /mnt
```

Reboot to u-boot and set up boot command:

```
Tegra124 (Jetson TK1) # setenv bootcmd 'fatload mmc 0:5 $loadaddr ubldr.tk1; bootelf'
Tegra124 (Jetson TK1) # saveenv
Saving Environment to MMC...
Writing to MMC(0)... done
Tegra124 (Jetson TK1) # boot
```

That's it. Also if I need to revert back to netbooting TK1 I can just set loaderdev to "net" in u-boot:

```
Tegra124 (Jetson TK1) # setenv loaderdev net; boot
```
