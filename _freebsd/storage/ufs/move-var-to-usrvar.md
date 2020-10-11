---
title: Move /var to /usr/var
date: 2010-10-31T17:50:39+00:00
author: Jake
layout: default
parent: UFS
grand_parent: Storage
permalink: /cheat-sheets/move-var-to-usrvar.html
categories:
  - Cheat Sheets
  - Configure FreeBSD
  - System Configuration
tags:
  - freebsd server
  - noschg
  - partition full
  - UAMP
---
# Move /var to /usr/var

Moving the `/var` partition to `/usr/var` can be a very good KISS technique for maintaining your FreeBSD filesystem. If you installed FreeBSD using the auto partition disk label in sysinstall you will have a separate `/var` partition of a fixed size. This can be quite undesirable, if for example, you are setting up a LAMP server (or UAMP, if you don't like getting lumped with Linux users) for a customer who doesn't  yet know how they are going to be using their available disk space. It could be used up mainly with mail, web data, or MySQL databases, so we want to have the flexibility of any of these being able to use as much of the disk as they need.

The solution is to only create 3 disk labels when installing, `/` (root), `/usr`, and of course `SWAP`. Giving the majority of the disk to `/usr`. e.g. 160Gb HDD, `/` = 1Gb, `SWAP` = 4G, `/usr` = 155Gb (or what ever is left). It's a good idea to move `/var` to `/usr/var` as one of the first things you do after install, as at this point `/var` is only in a 1Gb partition. So don't go installing things yet as you will run into some nasty problems.

To move the `/var` partitions use the following steps, NB. Doing this in single user mode is recommended but not required.

#### 1. Move the `/var` directory to `/usr/var`

```sh
# mv /var /usr/
```

You will see some strange looking errors during the move but it's safe to ignore these.

#### 2. Move `/var/empty`
Next if you look in the `/var` directory left over you will notice all the file/dirs have been moved apart from `/var/empty`, which has been copied but not moved. This is due to a chflag that is set, note you will need to make sure the `sysctrl kern.securelevel` is set to `-1`.

```sh
$ sysctl kern.securelevel
kern.securelevel: -1
```

Now remove the noshg flag from /var/empty and remove the /var directory

```sh
# chflags noschg /var/empty
# rm -r /var
```

#### 3. Link `/var` to `/usr/var`
At this point the /var directory has been fully moved to /usr/var so now we create a symlink so everything can find and use it.

```sh
# ln -s /usr/var /var
```

You can now reboot your system and everything should work as normal, but now /var is mapped to /usr! Now if you want you can use all your disk space for MySQL without changing the default location, like-wise this mail, or anything else that stores it's data in /var.

---
Note: If you already have postfix installed when you do this you might need to fix some permissions, e.g. `postfix set-permissions`
---

If you changed kern.securelevel to do this remember to put it back :)