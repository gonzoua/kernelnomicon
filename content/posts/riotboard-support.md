---
title: RIoTboard support
date: '2015-11-12T20:28:13'
draft: false
author: Oleksandr Tymoshenko
categories:
- Uncategorized
---

My career as [a trendy videoblogger](https://www.youtube.com/playlist?list=PLIlNZLQQ4dQvD_WThzImhmgI5477APYN4) starts to pay off. Nice people from [Newark element14](http://www.newark.com) offered to send me some hardware for experiments (no strings attached) I took them up on their offer and few days later received [RIoTboard](http://www.element14.com/community/community/designcenter/single-board-computers/riotboard). It's iMX6 Solo in developer-friendly package, not as compact as Beaglebone but nicely built and comes with more connectors. 

FreeBSD's iMX6 support is very good, so it took two one-line fixes to FreeBSD kernel code to make it work on RIoTboard. The other chunk of work was U-Boot package. Took more time than it should have due to some operator errors. The bring up process is more or less the same as for any other iMX6 system, so it should be really easy to add this board to [crochet](https://github.com/freebsd/crochet). Step by step it looks like this (some of the code came from crochet):

Prepare environment for the build

```
export TARGET=arm
export TARGET_ARCH=armv6
export SRCROOT=/src/FreeBSD/head
export MAKEOBJDIRPREFIX=/src/FreeBSD/obj
export MAKESYSPATH=$SRCROOT/share/mk
export KERNCONF=IMX6
```

Build world and kernel

```
make -j16 -C $SRCROOT buildworld
make -C $SRCROOT KERNCONF=$KERNCONF -j16 buildkernel
```

Create and partition SD card image

```
IMG=/tmp/SD.img

rm -f $IMG
dd if=/dev/zero of=$IMG bs=1000000 count=1024
MDUNIT=$(mdconfig -a -f $IMG)
DEV=/dev/$MDUNIT
gpart create -s mbr $DEV

# Create FAT partition
FAT_DEV=/dev/${MDUNIT}s1
gpart add -a 63 -b 16384 -s 50m -t '!12' $DEV
gpart set -a active -i 1 $DEV
newfs_msdos -L "BOOT" -F 16 ${FAT_DEV}

# Create UFS partition
UFS_DEV=/dev/${MDUNIT}s2
gpart add -t freebsd  $DEV
gpart create -s BSD $UFS_DEV
gpart add -t freebsd-ufs -a 64k $UFS_DEV

UFS_PART=/dev/${MDUNIT}s2a
newfs $UFS_PART
# Turn on Softupdates
tunefs -n enable $UFS_PART
tunefs -j enable -S 4194304 $UFS_PART
# Turn on NFSv4 ACLs
tunefs -N enable $UFS_PART

mdconfig -d -u $MDUNIT
```

Mount UFS partition

```
MDUNIT=$(sudo mdconfig -a -f $IMG)
DEV=/dev/$MDUNIT
UFS_DEV=/dev/${MDUNIT}s2a

sudo mount $UFS_DEV $MNTDIR
```

Install world + kernel

```
sudo -E make -C $SRCROOT installworld -DDB_FROM_SRC DESTDIR=$MNTDIR
sudo -E make -C $SRCROOT distribution -DDB_FROM_SRC DESTDIR=$MNTDIR
sudo -E make -C $SRCROOT installkernel DESTDIR=$MNTDIR
```

Install come configuration. We need hw.fdt.console in loader.conf because riotboard's DTS file does not have stdout-path property in chosen node. In this case FreeBSD falls back to serial0 node but user-accessible UART on RIoTboard is UART2 so as a result no kernel output visible on serial port. 

```
echo 'hw.fdt.console="/soc/aips-bus@02100000/serial@021e8000"' > /tmp/loader.conf
cat > /tmp/fstab <<__EOF__
/dev/mmcsd0s2a  /               ufs rw,noatime          1 1
/dev/mmcsd0s1   /boot/msdos     msdosfs rw,noatime      0 0
tmpfs           /tmp            tmpfs rw,size=31457280  0 0
tmpfs           /var/log        tmpfs rw,,size=15728640 0 0
tmpfs           /var/tmp        tmpfs rw,size=5242880   0 0
__EOF__
cat > /tmp/rc.conf <<__EOF__
hostname="riotboard"
sshd_enable="YES"

# minimal network config
ifconfig_ffec0="DHCP"

# turn off sendmail
sendmail_submit_enable="NO"
sendmail_outbound_enable="NO"
sendmail_msp_queue_enable="NO"
__EOF__

sudo mv /tmp/loader.conf $MNTDIR/boot/loader.conf
sudo mv /tmp/fstab $MNTDIR/etc/fstab
sudo mv /tmp/rc.conf $MNTDIR/etc/rc.conf
sudo mkdir $MNTDIR/boot/msdos
```

Unmount UFS and mount FAT partition

```
sudo umount $MNTDIR

FAT_DEV=/dev/${MDUNIT}s1
sudo mount -t msdosfs $FAT_DEV $MNTDIR
```

Build and install ubldr, unmount FAT partition

```
buildenv=`make -C $SRCROOT TARGET_ARCH=armv6 buildenvvars`
eval $buildenv make -C $SRCROOT/sys/boot -m $MAKESYSPATH obj
eval $buildenv make -C $SRCROOT/sys/boot -m $MAKESYSPATH clean
eval $buildenv make -C $SRCROOT/sys/boot -m $MAKESYSPATH depend
eval $buildenv make -C $SRCROOT/sys/boot -m $MAKESYSPATH UBLDR_LOADADDR=0x12000000 all

sudo cp ${MAKEOBJDIRPREFIX}/arm.armv6/${SRCROOT}/sys/boot/arm/uboot/ubldr $MNTDIR
sudo umount $MNTDIR
```

Download and install [u-boot-riotboard port](https://github.com/gonzoua/experimental-freebsd-ports/tree/master/sysutils/u-boot-riotboard). Then flash u-boot to the image

```
sudo dd if=/usr/local/share/u-boot/u-boot-riotboard/u-boot.imx of=$DEV bs=1k oseek=1 conv=sync
```

Detach md device

```
sudo mdconfig -d -u $MDUNIT
```

Configure boot switch selector on board to boot from SD card as described in [this document](http://riotboard.org/download/Wiki-Beginner-Boot%20Switch%20Configuration.pdf)

Insert SD card to either SD slot or uSD slot. When powered up you should see u-boot prompt

```
=> 
```

 

Boot command would look something like:

```
=> fatload mmc 0 $loadaddr ubldr
=> bootelf
```

For SD slot mmc unit is 0, for uSD it's probably 1, I didn't test. 

Boot log: [here](https://people.freebsd.org/~gonzo/arm/riotboard.dmesg.txt)

Next step for me is to get my [imx6_video](https://github.com/gonzoua/freebsd/tree/imx6_video) branch up to date and see if I can commit it to HEAD.
