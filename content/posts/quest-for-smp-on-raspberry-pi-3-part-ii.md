---
title: Quest for SMP on Raspberry Pi 3, part II
date: '2016-11-25T15:47:14'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

**Update (11/29/2016): DTB overlay no longer required. PSCI monitor will patch in-memory DTB to add psci node. [Commit](https://github.com/gonzoua/rpi3-psci-monitor/commit/0d65e734e5d03f91bc10451c4f510f60e5b90bc5)**

### Short version of how to get RPi3 with SMP support

- Build GENERIC arm64 kernel for your image, not GENERIC-UP
- Copy [https://people.freebsd.org/~gonzo/arm/rpi3-smp/u-boot.bin](https://people.freebsd.org/~gonzo/arm/rpi3-smp/u-boot.bin) and [https://people.freebsd.org/~gonzo/arm/rpi3-smp/armstub8.bin](https://people.freebsd.org/~gonzo/arm/rpi3-smp/armstub8.bin) to FAT partition on SD card.
- Copy [https://people.freebsd.org/~gonzo/arm/rpi3-smp/psci.dtbo](https://people.freebsd.org/~gonzo/arm/rpi3-smp/psci.dtbo) to `overlays` directory on FAT partition
- Edit config.txt: change value of device_tree_address and add psci overlay. These two lines should be there:

```
device_tree_address=0x4000
```
- Reboot device. It should boot with all four cores enabled and reboot should work too.

### Long version of what's in those .bin/.dtbo files

Boot sequence for PSCI monitor on RPi3 looks like this: 

- entry point is called at EL3
- monitor performs some CPU-specific initializations
- monitor sets up exceptions vector with SMC handler
- monitor reserves memory so it's not going to be overwritten by next boot stage
- monitor drops to EL2, passes control to next boot stage

First problem arises at step #1. If you generate binary and boot it using kernel parameter in config.txt by the time control is passed to entry point CPU is already at EL2. It's because VideoCore firmware does not pass control directly to kernel but runs some built-in code prior to that which is called ARM stub. Good news - that built-in code can be overridden. The history behind ARM stubs can be found in [this thread](https://github.com/raspberrypi/firmware/issues/579). And source code for default ARMv8 stub is in [armstub8.S](https://github.com/raspberrypi/tools/blob/master/armstubs/armstub8.S) file. 
It can be overridden either by adding armstub=NNN.bin line to config.txt or by copying stub to armstub8.bin file on FAT partition, this file is loaded automatically if it's present. 

Default stub performs some initialization and runs following pseudocode on each CPU:

```
void *spin_entry[4];
drop_to_el2();
if (current_cpu == 0)
    boot_kernel();
else {
    while (spin_entry[current_cpu] == 0) {
        wait_for_events();
    }
    jump_to(spin_entry[current_cpu]);
}
```

It's a good starting point so I added exception vector that jumps to PSCI handler routine, and PSCI entry points for PSCI_VERSION, CPU_ON, SYSTEM_REBOOT, SYSTEM_OFF, SYSTEM_RESET. 

So far so good. Back to memory reservation. My initial idea was for PSCI to edit DTB blob. Either add one more `memreserve` block that starts at 0x0 and 16Kb long or edit `/memory` node and cut 16Kb from first `reg` pair. I ended up implementing both of these approaches and neither worked. Because on RPi3 boot sequence looks like: u-boot -> loader.efi -> kernel. All memory information is passed to kernel via EFI and this part of DTB is just ignored. And U-Boot does not care about FDT either. It gets available memory size by querying VideoCore directly using mailbox API and then initializes on DRAM bank: [0..dram_size].
 
After short while of heavy thinking I ended up with just passing amount of reserved memory as the second argument of U-Boots entry function and then using that amount to start DRAM bank range with. Hackish but works. 

Now there is a matter of VideoCore-loaded FDT blob. Most of the FreeBSD images out there have device_tree_address=0x100 line in config.txt. This is reasonable value so FDT blob does not overlap with u-boot. Unfortunately new ARM stub with PSCI functionality is larger than 0x100 bytes and now overlaps with it. So  device_tree_address has to be bumped to 0x4000. But U-Boot has 0x100 hardcoded as default so config.txt and u-boot now out of sync. On the other hand ARM stub (default and PSCI-enabled) does pass FDT blob address as the first argument to entry function. So u-boot can set value of fdt_addr_r to correct address dynamically. 

PSCI: check, U-Boot: check. There is one more missing part. FreeBSD kernel identifies presence of PSCI by checking for FDT node with "arm,psci-0.2" compatibility string. Obviously there is no such thing in original dtb file. PSCI monitor adds this node dynamically by patching DTB blob in-memory before passing control to U-Boot. ~~It would be natural to make PSCI monitor patch FDT blob and add this new node but I wasn't up to the task of implementing it in assembler. I took the low road and just created DTB overlay with psci node. That's why psci.dtbo and "dtoverlay=psci" line in config.txt are required. I'm planning to add C files to PSCI monitor and implement dynamic DTB patching but for now overlay should do.~~  

Source code for all the stuff in this post: [rpi3-psci-monitor](https://github.com/gonzoua/rpi3-psci-monitor) and [u-boot patches](https://people.freebsd.org/~gonzo/arm/rpi3-smp/u-boot-patches/).

Precompiled bits: [rpi3-smp](https://people.freebsd.org/~gonzo/arm/rpi3-smp/)
