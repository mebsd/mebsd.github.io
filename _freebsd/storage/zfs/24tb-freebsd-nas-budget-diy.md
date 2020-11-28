---
title: 24Tb FreeBSD NAS budget DIY
date: 2011-07-05T23:53:15+01:00
author: Jake
layout: default
parent: ZFS
grand_parent: Storage
permalink: /make-build-your-freebsd-word/24tb-freebsd-nas-budget-diy.html
categories:
  - Build Your FreeBSD World
  - Storage
tags:
  - DIY NAS
  - storage
  - ZFS
---
# 24Tb FreeBSD NAS budget DIY

I had a bunch of 3Tb disks come in and thought it would be a good opportunity to see how many I could join together.

I used 9 WD Green 3Tb hard disks, 1 WD Blue 500Gb to run FreeBSD 8.2 amd64. The mother board is a Gigabyte Z68-D3-B3, the number of SATA ports are extended using the 2 port RocketRaid PCI express cards the come with the 3Tb disks. Chucked as much RAM in as I could, think it was 8Gb.

  * 9 x WD Green 3Tb hard disks
  * 2 x WD Blue 500Gb (OS mirror).
  * Gigabyte Z68-D3-B3,
  * 3 x 2 port RocketRaid PCI express cards (come with the 3Tb disks)
  * 8Gb RAM

The RocketRaid cards (Free with the hard drives!)
![24Tb FreeBSD NAS rocketraid 62x](/assets/images/freebsd/zfs/24Tb-FreeBSD-NAS-rocketraid-62x1.jpg){:class="img-responsive"}

The mother board Gigabyte Z68-D3-B3
![24Tb FreeBSD NAS Gigabyte Z68-D3-B3](/assets/images/freebsd/zfs/24Tb-FreeBSD-NAS-motherboard.jpg){:class="img-responsive"}
![24Tb FreeBSD NAS motherboard Gigabyte Z68-D3-B3](/assets/images/freebsd/zfs/24Tb-FreeBSD-NAS-motherboard-2.jpg){:class="img-responsive"}

The complete server ready for FreeBSD 9.0 install ZFS v28!
![24Tb FreeBSD NAS complete](/assets/images/freebsd/zfs/24Tb-FreeBSD-NAS-complete.jpg){:class="img-responsive"}

This is the result of the setup.

![24Tb FreeBSD NAS setup](/assets/images/freebsd/zfs//3tzfs11.jpg){:class="img-responsive"}
![WD Green 3Tb disks](/assets/images/freebsd/zfs/3tzfs2.jpg){:class="img-responsive"}

I run a basic install of FreeBSD 8.2 RELEASE amd64 with zfs v15 to create a 24Tb striped zpool.

```sh
# zpool create tank /dev/ada0 /dev/ada1 /dev/ada2 /dev/ada3 /dev/ada4 /dev/ada5 /dev/ada6 /dev/ada7 /dev/ada8
# zfs create tank/data
```

![24Tb FreeBSD zfs](/assets/images/freebsd/zfs/3tzfs3.jpg){:class="img-responsive"}