---
id: 454
title: Resize growfs UFS partition inside ZFS zvol
date: 2011-11-26T22:08:19+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=454
permalink: /cheat-sheets/resize-growfs-ufs-partition-inside-zfs-zvol.html
parent: UFS
grand_parent: Storage
categories:
  - Cheat Sheets
  - Commands
  - Configure FreeBSD
  - Storage
  - Storage Commands
tags:
  - freebsd-ufs
  - partition full
  - storage
  - ZFS
---
## Resize growfs UFS partition inside ZFS zvol

In ZFS there is a very useful feature for creating a raw block devices, that can be used just like a physical hard drive for just about anything.

Here's an example of how to create a zvol in ZFS, the difference between creating a zvol and a normal zfs filesystem is just the `-V size` option.

```sh
# zfs list
NAME        USED  AVAIL  REFER  MOUNTPOINT
tank        103G   354G    21K  none
tank/root  1.06G   354G   573M  /
tank/swap  19.2G   358G  4.01G  -
tank/usr   78.2G   354G  9.43G  /usr
tank/var   1.11G   354G   128M  /var
# zfs create -p -V 20G tank/sandbox/zvol_ufs
# zfs list
NAME                    USED  AVAIL  REFER  MOUNTPOINT
tank                    124G   333G    21K  none
tank/root              1.06G   333G   573M  /
tank/sandbox           20.6G   333G    31K  none
tank/sandbox/zvol_ufs  20.6G   354G    16K  -
tank/swap              19.2G   337G  4.01G  -
tank/usr               78.2G   333G  9.43G  /usr
tank/var               1.11G   333G   128M  /var
# ls -l /dev/zvol/tank/sandbox/zvol_ufs
crw-r-----  1 root  operator   11,  91 Nov 26 21:06 /dev/zvol/tank/sandbox/zvol_ufs
```

Now we have a 20G zvol, a raw block device ready to be formatted with a file system. In this case we are putting a UFS partition on it.

```sh
# newfs -U -O2 /dev/zvol/tank/sandbox/zvol_ufs
/dev/zvol/tank/sandbox/zvol_ufs: 20480.0MB (41943040 sectors) block size 16384, fragment size 2048
	using 112 cylinder groups of 183.72MB, 11758 blks, 23552 inodes.
	with soft updates
super-block backups (for fsck -b #) at:
 160, 376416, 752672, 1128928, 1505184, 1881440, 2257696, 2633952, 3010208, 3386464, 3762720, 4138976, 4515232, 4891488, 5267744, 5644000, 6020256, 6396512, 6772768, 7149024, 7525280, 7901536, 8277792, 8654048,
 9030304, 9406560, 9782816, 10159072, 10535328, 10911584, 11287840, 11664096, 12040352, 12416608, 12792864, 13169120, 13545376, 13921632, 14297888, 14674144, 15050400, 15426656, 15802912, 16179168, 16555424,
 16931680, 17307936, 17684192, 18060448, 18436704, 18812960, 19189216, 19565472, 19941728, 20317984, 20694240, 21070496, 21446752, 21823008, 22199264, 22575520, 22951776, 23328032, 23704288, 24080544, 24456800,
 24833056, 25209312, 25585568, 25961824, 26338080, 26714336, 27090592, 27466848, 27843104, 28219360, 28595616, 28971872, 29348128, 29724384, 30100640, 30476896, 30853152, 31229408, 31605664, 31981920, 32358176,
 32734432, 33110688, 33486944, 33863200, 34239456, 34615712, 34991968, 35368224, 35744480, 36120736, 36496992, 36873248, 37249504, 37625760, 38002016, 38378272, 38754528, 39130784, 39507040, 39883296, 40259552,
 40635808, 41012064, 41388320, 41764576
# mount /dev/zvol/tank/sandbox/zvol_ufs /mnt
# df
Filesystem                                 1K-blocks     Used     Avail Capacity  Mounted on
tank/root                                  349754461   586290 349168171     0%    /
devfs                                              1        1         0   100%    /dev
tank/usr                                   359056973  9888802 349168171     3%    /usr
tank/var                                   349299471   131300 349168171     0%    /var
/dev/zvol/tank/sandbox/zvol_ufs             20308398        4  18683724     0%    /mnt
```

Now we have the UFS filesystem setup and mounted we are going to fill it with some data. I am going to dd a 2Gb file from `/dev/random` and echo a simple string into a file just to show we can read it after the growfs resize.

```sh
# cd /mnt
# dd if=/dev/random of=/mnt/file.out bs=1m count=2000
2000+0 records in
2000+0 records out
2097152000 bytes transferred in 49.641022 secs (42246350 bytes/sec)
# echo hello > hello
# ls -ltr
total 2049026
drwxrwxr-x  2 root  operator         512 Nov 26 21:17 .snap
-rw-r--r--  1 root  wheel     2097152000 Nov 26 21:19 file.out
-rw-r--r--  1 root  wheel              6 Nov 26 21:20 hello
```

OK now we have some data in it, we will see if we can grow the zvol and UFS partition from 20Gb to 40Gb. First we will use the `zfs set volsize` command to resize the zvol.

```sh
# zfs get volsize tank/sandbox/zvol_ufs
NAME                   PROPERTY  VALUE    SOURCE
tank/sandbox/zvol_ufs  volsize   20G      local
# zfs set volsize=40G tank/sandbox/zvol_ufs
# zfs list
NAME                    USED  AVAIL  REFER  MOUNTPOINT
tank                    124G   312G    21K  none
tank/root              1.06G   312G   573M  /
tank/sandbox           41.3G   312G  40.4K  none
tank/sandbox/zvol_ufs  41.3G   333G  2.22G  -
tank/swap              19.2G   316G  4.01G  -
tank/usr               78.2G   312G  9.43G  /usr
tank/var               1.11G   312G   128M  /var
```

Now the zvol has been resized we will use growfs to make the UFS partition fit into its zvol container. As you can see below the UFS partition is still the same size, it will have to be unmounted before growfs can do it's magic.

_Note: See this extract from growfs man page, if no size option is provided it will just grow it to the size of the entire partition, in our case the full size of the zvol._

```
          -s size
             Determines the size of the file system after enlarging in sec-
             tors.  This value defaults to the size of the raw partition spec-
             ified in special (in other words, growfs will enlarge the file
             system to the size of the entire partition).
```

```sh
# df
Filesystem                                 1K-blocks     Used     Avail Capacity  Mounted on
tank/root                                  349754461   586290 349168171     0%    /
devfs                                              1        1         0   100%    /dev
tank/usr                                   359056973  9888802 349168171     3%    /usr
tank/var                                   349299471   131300 349168171     0%    /var
/dev/zvol/tank/sandbox/zvol_ufs             20308398  2049030  16634698     0%    /mnt
# cd ~
# umount /mnt
# growfs /dev/zvol/data/sandbox/zvol_ufs
We strongly recommend you to make a backup before growing the Filesystem

 Did you backup your data (Yes/No) ? Yes
new file systemsize is: 20971520 frags
Warning: 357248 sector(s) cannot be allocated.
growfs: 40785.6MB (83528832 sectors) block size 16384, fragment size 2048
	using 222 cylinder groups of 183.72MB, 11758 blks, 23552 inodes.
	with soft updates
super-block backups (for fsck -b #) at:
 42140832, 42517088, 42893344, 43269600, 43645856, 44022112, 44398368, 44774624, 45150880, 45527136, 45903392, 46279648, 46655904, 47032160, 47408416, 47784672, 48160928, 48537184, 48913440, 49289696, 49665952,
 50042208, 50418464, 50794720, 51170976, 51547232, 51923488, 52299744, 52676000, 53052256, 53428512, 53804768, 54181024, 54557280, 54933536, 55309792, 55686048, 56062304, 56438560, 56814816, 57191072, 57567328,
 57943584, 58319840, 58696096, 59072352, 59448608, 59824864, 60201120, 60577376, 60953632, 61329888, 61706144, 62082400, 62458656, 62834912, 63211168, 63587424, 63963680, 64339936, 64716192, 65092448, 65468704,
 65844960, 66221216, 66597472, 66973728, 67349984, 67726240, 68102496, 68478752, 68855008, 69231264, 69607520, 69983776, 70360032, 70736288, 71112544, 71488800, 71865056, 72241312, 72617568, 72993824, 73370080,
 73746336, 74122592, 74498848, 74875104, 75251360, 75627616, 76003872, 76380128, 76756384, 77132640, 77508896, 77885152, 78261408, 78637664, 79013920, 79390176, 79766432, 80142688, 80518944, 80895200, 81271456,
 81647712, 82023968, 82400224, 82776480, 83152736
```

Now with the UFS partition resized we can now mount it again and check all our data is intact.

```sh
# mount /dev/zvol/data/sandbox/zvol_ufs /mnt
# df
Filesystem                                 1K-blocks     Used     Avail Capacity  Mounted on
tank/root                                  349754461   586290 349168171     0%    /
devfs                                              1        1         0   100%    /dev
tank/usr                                   359056973  9888802 349168171     3%    /usr
tank/var                                   349299471   131300 349168171     0%    /var
/dev/zvol/tank/sandbox/zvol_ufs             40450092  2049030  35165056     0%    /mnt
# cd /mnt
# cat hello
hello
# ls -ltr
total 2049028
drwxrwxr-x  2 root  operator         512 Nov 26 21:17 .snap
-rw-r--r--  1 root  wheel     2097152000 Nov 26 21:19 file.out
-rw-r--r--  1 root  wheel              6 Nov 26 21:20 hello
```