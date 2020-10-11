---
title: Duplicate ZFS pool over SSH
author: Jake
layout: default
parent: ZFS
grand_parent: Storage
permalink: /cheat-sheets/duplicate-zfs-pool-over-ssh-freebsd.html
categories:
  - Cheat Sheets
  - Configure FreeBSD
  - Storage
tags:
  - gpart
  - gpt
  - network
  - storage
  - ZFS
---
## Duplicate ZFS pool over SSH

To duplicate a zfs pool over ssh you must first create a recursive snapshot of the pool you want to copy

```sh
# zfs snapshot -r tank@fullbackup
# zfs list -t snapshot
NAME                              USED  AVAIL  REFER  MOUNTPOINT
tank@fullbackup                      0      -    21K  -
tank/root@fullbackup              136K      -   573M  -
tank/swap@fullbackup                 0      -    16K  -
tank/tmp@fullbackup                  0      -  5.28M  -
tank/usr@fullbackup               182K      -  6.26G  -
tank/var@fullbackup               352K      -   116M  -
```

On the target server setup the target pool

```sh
# zpool create tank mirror gpt/disk01 gpt/disk02
# zpool status
  pool: tank
 state: ONLINE
 scan: none requested
config:

	NAME            STATE     READ WRITE CKSUM
	tank            ONLINE       0     0     0
	  mirror-0      ONLINE       0     0     0
	    gpt/disk00  ONLINE       0     0     0
	    gpt/disk01  ONLINE       0     0     0

errors: No known data errors
```

Now make sure you can ssh to the target server from the source server, you will need to do this as root. You may need to edit your `/etc/ssh/sshd_config` and set `PermitRootLogin yes`. Then restart ssh, `killall -1 sshd` or `/etc/rc.d/sshd restart`.

Make sure you can login

```sh
# ssh root@targetserver.com
```

If this works you can now send the pool over ssh to the target server.

```sh
# zfs send -R tank@fullbackup | ssh targetserver.com "zfs recv -vFd tank"
```

Once finished your target zpool will look like this

```sh
# zfs list -t all
NAME                              USED  AVAIL  REFER  MOUNTPOINT
tank                              127G   145G    21K  none
tank@fullbackup                      0      -    21K  -
tank/root                         573M   145G   573M  /mnt
tank/root@fullbackup                 0      -   573M  -
tank/swap                        4.00G   149G    16K  -
tank/swap@fullbackup                 0      -    16K  -
tank/tmp                         5.28M   145G  5.28M  /mnt/tmp
tank/tmp@fullbackup                  0      -  5.28M  -
tank/usr                         6.40G   145G  6.26G  /mnt/usr
tank/usr@fullbackup                  0      -  6.26G  -
tank/var                          116M   145G   116M  /mnt/var
tank/var@fullbackup                  0      -   116M  -
```

If you wish you can delete the snapshot.

If you plan on booting of the target pool you will also need to set the bootfs on the pool

```sh
# zpool set bootfs=tank/root tank
```

Edit:  
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
```

Then:

```sh
# cp /boot/zfs/zpool.cache /mnt/boot/zfs/
```

Now you can reboot