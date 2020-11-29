---
title: 'Keeping ownerships and permissions'
date: 2010-12-20T19:03:41+00:00
author: Jake
layout: default
permalink: /cli-commands/rsync-over-ssh-keeping-file-ownerships-and-permissions.html
parent: Rsync
categories:
  - Commands
  - Network Commands
tags:
  - backup server
  - file permissions
  - freebsd-ufs
---
# Rsync over SSH - Keeping file ownerships and permissions'

One of the simplist and most secure way of backing up your data across the network is rsync via ssh.

One of the most frustrating things, however, is restoring your much needed backup to find the file permissions and ownership is that of which ever ssh user was used to call the rsync. This can be a disaster if restoring system files or some ones web site with all sorts of custom chmods.

Why does this happen? The answer simply is that rsync was connecting to the backup server as root (or a user with root privileges).

Unless you are ssh'ing as root, which I'm sure you're not as we all know how silly allowing root ssh to the server is, right? So we need a secure solution, so here comes sudo to save the day!

Consider the following rsync command:

`# rsync -a /usr/local/www ssh-user@backup-server.tld:/usr/backup_dir/`

This will copy the /usr/local/www directory from the server we are running rsync from to the backup server as user `ssh-user`. It wont keep any special permissions or ownerships, just the default umask and owned by ssh-user, ssh-user does not have the access, just try sshing in as that user and doing a chown root:wheel www. If you can't do it rsync can't do it, we use sudo to give us the rights.

First you'll need to have the following in your sudoers config.

`# visudo`

```
Cmnd_Alias      RSYNC = /usr/local/bin/rsync

ssh-user        ALL = NOPASSWD: RSYNC
```

Now we have the rights we need to tell rsync how to use sudo, and therefor keep permissions. Simply done like this:

`# rsync -a --rsync-path="sudo rsync" /usr/local/www ssh-user@backup-server.tld:/usr/backup_dir/`

The extra flag, `--rsync-path`, tells rsync to run `sudo rsync` when it gets to the backup server instead of `rsync`.