---
id: 527
title: Get HDD serial number in FreeBSD
date: 2012-01-20T12:28:38+00:00
author: Jake
layout: post
guid: http://www.mebsd.com/?p=527
permalink: /make-build-your-freebsd-word/get-hdd-serial-number-in-freebsd.html
parent: Storage
grand_parent: freebsd
categories:
  - Build Your FreeBSD World
  - Cheat Sheets
  - Configure FreeBSD
  - Storage
tags:
  - HDD
  - storage
---
## Get HDD serial number in FreeBSD

It can be very useful to know the serial number of a HDD. Say you have a FreeBSD server with 10+ hard drives, all the same make/model/size and one of the hard drives fails, how to you know which disk you need to unplug to replace it? On the sticker on HDD is the unique serial number of the HDD, so by matching this to the serial number reported from the failed disk dev you know which one to pull.

Here's an example, the serial number on the physical HDD will look something like this:

![Seagate barracuda serial number location](/assets/images/freebsd/zfs/Seagate-barracuda-serial-number1.jpg){:class="img-responsive"}

On the command line, to see the serial number of an attached HDD you can use the `camcontrol identify` command, if you are using AHCI, `ATA_CAM kernel option`. This can usually be enabled by putting `ahci_load="YES"` in `/boot/loader.conf` and reboot.

```
# camcontrol identify ada0

pass1:  ATA-7 SATA 1.x device
pass1: 150.000MB/s transfers (SATA 1.x, UDMA6, PIO 8192bytes)

protocol              ATA/ATAPI-7 SATA 1.x
device model          ST3200820AS
firmware revision     3.AAD
serial number         5QE0RCHD
cylinders             16383
heads                 16
sectors/track         63
sector size           logical 512, physical 512, offset 0
LBA supported         268435455 sectors
LBA48 supported       390721968 sectors
PIO supported         PIO4
DMA supported         WDMA2 UDMA6

Feature                      Support  Enabled   Value           Vendor
read ahead                     yes	yes
write cache                    yes	yes
flush cache                    yes	yes
overlap                        no
Tagged Command Queuing (TCQ)   no	no
Native Command Queuing (NCQ)   yes		32 tags
SMART                          yes	yes
microcode download             yes	yes
security                       yes	no
power management               yes	yes
advanced power management      no	no
automatic acoustic management  no	no
media status notification      no	no
power-up in Standby            no	no
write-read-verify              yes	no	0/0x0
unload                         no	no
free-fall                      no	no
data set management (TRIM)     no
```

Look for the `serial number` field, as you can see the output from camcontrol on ada0 matches the picture above. Run this command against your failed HDD, assuming it isn't really really broken in which case you will need to run it against all the other disks and go by process of elimination. If none of your disks have failed yet, it might be a good idea to record which serial number goes with which disk device, so when it does fail you know which S/N to look for.