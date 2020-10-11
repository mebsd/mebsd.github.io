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
image: /wp-content/uploads/2012/01/Seagate-barracuda-serial-number1.jpg
categories:
  - Build Your FreeBSD World
  - Cheat Sheets
  - Configure FreeBSD
  - Storage
tags:
  - HDD
  - storage
---
It can be very useful to know the serial number of a HDD. Say you have a FreeBSD server with 10+ hard drives, all the same make/model/size and one of the hard drives fails, how to you know which disk you need to unplug to replace it? On the sticker on HDD is the unique serial number of the HDD, so by matching this to the serial number reported from the failed disk dev you know which one to pull.

Here&#8217;s an example, the serial number on the physical HDD will look something like this:

<p style="text-align: center;">
  <a href="https://mebsd.com/wp-content/uploads/2012/01/Seagate-barracuda-serial-number1.jpg" data-rel="lightbox-image-0" data-rl_title="" data-rl_caption="" title=""><img loading="lazy" class="aligncenter  wp-image-531" title="Seagate barracuda serial number location" src="http://www.mebsd.com/wp-content/uploads/2012/01/Seagate-barracuda-serial-number1-1024x768.jpg" alt="Seagate barracuda serial number location" width="614" height="461" srcset="https://mebsd.com/wp-content/uploads/2012/01/Seagate-barracuda-serial-number1-1024x768.jpg 1024w, https://mebsd.com/wp-content/uploads/2012/01/Seagate-barracuda-serial-number1-300x225.jpg 300w, https://mebsd.com/wp-content/uploads/2012/01/Seagate-barracuda-serial-number1.jpg 2048w" sizes="(max-width: 614px) 100vw, 614px" /></a>
</p>

&nbsp;

On the command line, to see the serial number of an attached HDD you can use the &#8216;camcontrol identify&#8217; command, if you are using AHCI, &#8220;ATA\_CAM kernel option&#8221;. This can usually be enabled by putting ahci\_load=&#8221;YES&#8221; in /boot/loader.conf and reboot.

<pre class="console-text notranslate"># camcontrol identify ada0

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
data set management (TRIM)     no</pre>

Look for the &#8216;serial number&#8217; field, as you can see the output from camcontrol on ada0 matches the picture above. Run this command against your failed HDD, assuming it isn&#8217;t really really broken in which case you will need to run it against all the other disks and go by process of elimination. If none of your disks have failed yet, it might be a good idea to record which serial number goes with which disk device, so when it does fail you know which S/N to look for.