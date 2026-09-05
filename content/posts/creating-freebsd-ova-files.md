---
title: Creating FreeBSD OVA files
date: '2020-01-19T16:13:17'
draft: false
author: Oleksandr Tymoshenko
categories:
- FreeBSD
---

If you want to jump right into the action check out [freebsd-mkova](https://github.com/gonzoua/freebsd-mkova) repo. Below are some technical details on what OVA is and its internals.

In addition to installation media like DVD or memstick, FreeBSD RE ships FreeBSD releases as a virtual disk image in a number of formats. This is a convenient way when you start your whole VM using bhyve or qemu, as a single CLI command and pass the image location as an argument. It's less handy if you try to create VM using GUI-based tools like VMWare or VirtualBox. You need to create VM and then configure it to use the image as a drive. Not an awful lot of work but still.

At my $DAYJOB I came across another way of shipping VM-based products called virtual appliance. Essentially it's a pre-configured virtual machine with disk image included in a self-contained file that can be imported by hypervisor software using couple of mouse clicks or a single command like **VBoxManage import**. The file format for this kind of deliverables is OVA

Internally OVA is a tar(1) archive that includes OVF file,  disk image or images in VMDK format, and optional manifest (list of files with checksums). OVF file is an XML file with VM description that looks like [this](https://people.freebsd.org/~gonzo/sample.ovf.txt).  More information on the format can be found at [https://www.dmtf.org/standards/ovf](https://www.dmtf.org/standards/ovf). 

So on the surface, we need to take official VMDK, create XML file and package them both in .tar archive. Unfortunately, there is a problem: VMDK format comes in several flavors and while FreeBSD RE ships monolithic sparse, OVA needs the image in stream-optimized flavor.

As the name implies monolithic-sparse format does not have every single sector of the virtual disk in the file, it only stores the data that was written by OS. The standard unit of data on disk is a sector, usually 512 bytes long.  VMDK organizes sectors in grains: a sequence of sectors. The size of the grain (in sectors) is defined in the VMDK header as a *grainSize* field. So if an image is N sectors long there are N/*grainSize* grains. Only grains with the content that is different from all-zeroes are stored in the image file. To organize fast random access to the stored data in the image VMDK maintains a two-level index:  grain directory and grain table. Grain table is a fixed-size array (*numGTEsPerGT* field in the header) with offsets of the grain in VMDK. If offset is zero it means the grain is not present in the file and its content is all-zeroes. Grain tables' offsets, in turn, are stored in grain directory. And again if offset in the directory is zero it means none of the grains in the table is present in the image. The sequence of steps  to read sector X from the VMDK is approximately this:

```
grainIdx := [X / grainSize]                 // Find the grain index 
gtIdx := [grainIdx / numGTEsPerGt]          // Find the grain table index
gtOffset := grainDirectory[gtIdx]           // Find grain table offset                                      // if gtOffset is zero return empty sector
grainTable := readAt(gtOffset)              // read grain table data 
grainOffset := grainTable[X % grainSize]    // find grain offset                                             // if grainOffset is zero return empty sector
sectorData := readAt(grainOffset + (X % grainSize)) // read sector data
```

The VMDK file layout for monolithic sparse format would look like:

[header] [imageDescriptor] [GrainDirectory] [GrainTable0] ... [GrainTableN ] [ Grain0 ] [GrainX ] [ GrainY ] ... [ GrainZ

The stream-optimized VMDK operates with the same concepts: grain, grain table, grain directory but organizes them differently. Again as the name implies it's optimized for streaming which means no random access to the image file so it lays out elements a bit differently. GD, GT, grains are prefixed with the markers and can be detected as the software reads file from the network. Also, grains can be compressed to save bandwidth. The stream-optimized VMDK layout looks like this:

[ header ] [imageDescriptor] [GrainMarker] [Grain] ... [GrainMarker] [Grain] [GrainTableMarker] [GrainTable0] ... [GrainDirectoryMarker] [GrainDirectory] [FooterMarker][FinalHeader] [EndOfStreamMarker]

the FinalHeader is the copy of the header with the *gdOffset* field set to point to [GrainDirectory] location

 As you can see the conversion from monolithic sparse to stream optimized is quite straightforward. I spent some time implementing it over the holiday break, added OVF generation logic and shipped [freebsd-mkova](https://github.com/gonzoua/freebsd-mkova). I wanted to write it in Go to practice a new language but was turned off by the XML generation and namespaces support in it, it felt unnecessary verbose and clunky. Will try to find some other project for Go my studies.
