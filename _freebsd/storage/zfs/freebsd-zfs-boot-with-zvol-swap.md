---
title: FreeBSD ZFS boot with zvol SWAP
author: Jake
layout: default
parent: ZFS
grand_parent: Storage
permalink: /cheat-sheets/freebsd-zfs-boot-with-zvol-swap.html
categories:
  - Cheat Sheets
  - Configure FreeBSD
  - Storage
  - System Configuration
tags:
  - big partition
  - ZFS
---
## FreeBSD ZFS boot with zvol SWAP

First use gpart to setup the disk partitions, in this set up we have 4 disks, `ad4` `ad6` `ad8` `ad10`.

We have booted from mfsbsd boot cd from <http://mfsbsd.vx.sk/>.  
First check for any existing partitions on the disks and remove them.

```sh
# gpart show
# gpart delete -i 2 ad4
# gpart delete -i 2 ad6
# gpart delete -i 2 ad8
# gpart delete -i 2 ad10
# gpart delete -i 1 ad10
# gpart delete -i 1 ad8
# gpart delete -i 1 ad4
# gpart delete -i 1 ad6
# gpart destroy ad4
# gpart destroy ad6
# gpart destroy ad8
# gpart destroy ad10
```

Now the disks are clean we will setup gpt on them.

```sh
# gpart show
# gpart create -s gpt /dev/ad4
# gpart create -s gpt /dev/ad6
# gpart create -s gpt /dev/ad8
# gpart create -s gpt /dev/ad10
```

fdisk them&#8230;

```sh
# echo "a 1" | fdisk -f - ad4
# echo "a 1" | fdisk -f - ad6
# echo "a 1" | fdisk -f - ad8
# echo "a 1" | fdisk -f - ad10
```

Now create a boot part on each disk:

```sh
# gpart add -t freebsd-boot -s 128 ad4
# gpart add -t freebsd-boot -s 128 ad6
# gpart add -t freebsd-boot -s 128 ad8
# gpart add -t freebsd-boot -s 128 ad10
```

Set the main partition on the disk for the zpool and set disk label:

```sh
# gpart add -t freebsd-zfs -s 278G -l disk00 ad4
# gpart add -t freebsd-zfs -s 278G -l disk01 ad6
# gpart add -t freebsd-zfs -s 278G -l disk02 ad8
# gpart add -t freebsd-zfs -s 278G -l disk03 ad10
```

```sh
# dd if=/dev/zero of=/dev/ad4p2 bs=512 count=560
# dd if=/dev/zero of=/dev/ad6p2 bs=512 count=560
# dd if=/dev/zero of=/dev/ad8p2 bs=512 count=560
# dd if=/dev/zero of=/dev/ad10p2 bs=512 count=560
```

Set the freebsd bootcode on the first partition.

```sh
# gpart bootcode -b /boot/pmbr -p /boot/gptzfsboot -i 1 ad4
# gpart bootcode -b /boot/pmbr -p /boot/gptzfsboot -i 1 ad6
# gpart bootcode -b /boot/pmbr -p /boot/gptzfsboot -i 1 ad8
# gpart bootcode -b /boot/pmbr -p /boot/gptzfsboot -i 1 ad10
```

Ok now we can create the zpool we will setup a raidz2 this time, the altroot is important so we don't mount over our current system.

If you are running zpool v28 you must also set the cache file path

```sh
# zpool create -f -m none -o altroot=/mnt -o cachefile=/boot/zfs/zpool.cache data raidz2 gpt/disk00 gpt/disk01 gpt/disk02 gpt/disk03
```

Other wise if zpool v15 or older use this command.

```sh
# zpool create -f -m none -o altroot=/mnt data raidz2 gpt/disk00 gpt/disk01 gpt/disk02 gpt/disk03
# zpool status
```

Now create the filesystems for freebsd, most of these are optional.

```sh
# zfs create -o mountpoint=/ data/root
# zfs create -o mountpoint=/usr data/usr
# zfs create -o mountpoint=/var data/var
# zfs create -o mountpoint=/var/db data/var/db
# zfs create -o mountpoint=/tmp data/tmp
# zfs create -o mountpoint=/usr/home data/usr/home
```

Setup zvol swap space:

```sh
# zfs create -V 4G data/root/swap
# zfs set org.freebsd:swap=on data/root/swap
# zfs set checksum=off data/root/swap
```

Set data/root as the boot filesystem.

```sh
# zpool set bootfs=data/root data
```

At this point you extract a freebsd distro into /mnt.  
Configure /boot/loader.conf and rc.conf

```sh
# echo 'zfs_load="YES"' >> /mnt/boot/loader.conf
# echo 'vfs.root.mountfrom="zfs:data/root"' >> /mnt/boot/loader.conf
# echo 'zfs_enable="YES"' >> /mnt/etc/rc.conf
```

Import and Export to make sure all changes are written to the disk and the copy the cache to the new pool.

```sh
# zpool export data
```

Again if you are using zpool v28 then add the cache file path

```sh
# zpool import -o altroot=/mnt -o cachefile=/boot/zfs/zpool.cache data

```

Other wise

```sh
# zpool import -o altroot=/mnt data
# cp /boot/zfs/zpool.cache /mnt/boot/zfs/
```

Now boot into full freebsd zfs system.