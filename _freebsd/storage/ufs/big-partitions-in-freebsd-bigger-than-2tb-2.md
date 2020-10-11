---
title: 'UFS partitions over 2Tb'
date: 2010-10-31T18:15:13+00:00
author: Jake
layout: default
parent: UFS
grand_parent: Storage
permalink: /configure-freebsd-servers/big-partitions-in-freebsd-bigger-than-2tb-2.html
categories:
  - Configure FreeBSD
  - System Configuration
tags:
  - big partition
  - fdisk
  - freebsd server
  - freebsd-ufs
  - gpart
  - gpt
  - max partition size
---
With disk sizes these days there is a good chance you are going to want to make some mega big partition using a several 2TB disks in some RAID configuration. Fdisk is not overly happy about this, and will restrict us to about 2TB partition even though we have many more sectors that could be used.

See this [quote](http://www.freebsd.org/projects/bigdisk/index.html)

> The UFS2 filesystem was introduced in 2003 as a replacement to the original UFS and provides 64 bit counters and offsets. This allows for files and filesystems to grow to `2^73 bytes (2^64 * 512)` in size and hopefully be sufficient for quite a long time. UFS2 largely solved the storage size limits imposed by the filesystem. Unfortunately, many tools and storage mechanisms still use or assume 32 bit values, often keeping FreeBSD limited to 2TB.

The best solution at the moment is to use gpart to make the partition, this this example with a 3 2TB disk RAID 0 array.

1. Create the partitioning scheme (/dev/ar1 is our RAID device in this case)

```sh
# gpart create -s gpt ar1
```

2. Add the new partition

```sh
# gpart add -t freebsd-ufs ar1
```

3. Now newfs the new partition (the bigger the disk the longer this will take)

```sh
# newfs -U /dev/ar1p1
```

4. That's it, you now have a big partition ready to mount

```sh
# mount /dev/ar1p1 /data
```

As you can see from this output of df -H, we have the full capacity of our disks

```sh
Filesystem     Size    Used   Avail Capacity  Mounted on
/dev/ad0s1a    1.0G    181M    773M    19%    /
devfs          1.0k    1.0k      0B   100%    /dev
/dev/ad0s1d    478G    5.3G    435G     1%    /usr
/dev/ar1p1     5.8T    1.0T    4.3T    19%    /data
```