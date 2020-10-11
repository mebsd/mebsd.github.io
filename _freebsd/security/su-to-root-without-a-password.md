---
title: Become root without a password or sudo
author: Jake
layout: default
parent: Security
grant_parent: FreeBSD
permalink: /cheat-sheets/su-to-root-without-a-password.html
categories:
  - Cheat Sheets
  - Configure FreeBSD
  - System Configuration
tags:
  - freebsd server
  - nopasswd
  - pam.d/su
  - passwordless root
  - su root no password
---
## Become root without a password or sudo
Now to start off I must say I'm not recommending that you do this, but can be good to know.

If you need to gain root privileges from a user account without a password on a system where you can't or would prefer not to install sudo, this method could be for you.

As any FreeBSD user knows, sudo is not installed by default with the standard distro, actually a good thing in my opinion. Not forcing extra software on you, instead just letting you decide what you need.

What we are going to do is basically the same thing as this config line for sudo

```sh
%wheel ALL=(ALL) NOPASSWD: ALL
```

Which says, any user in group `wheel` can execute anything, such as a shell, without a password.

The alternative way to this in FreeBSD 5.x and newer, **without** sudo, is to add a rule into `/etc/pam.d/su`

```sh
auth		sufficient 	pam_group.so 		trust use_uid
```

The contents of `/etc/pam.d/su` should now look something like this

```sh
# auth
auth		sufficient	pam_rootok.so           no_warn
auth		sufficient	pam_self.so             no_warn
auth		requisite	pam_group.so            no_warn group=wheel root_only fail_safe
auth		sufficient 	pam_group.so            trust use_uid
auth		include		system

# account
account		include		system

# session
session		required	pam_permit.so
```

Now make sure the user that requires the passwordless access is indeed in wheel. All that's left to do now is to test by su'ing to root, it should let you right in.

To run a one liner as root like you would with `sudo whoami` you can use this command

```sh
$ su -m root -c 'whoami'
```

Bit more to type but it does the job.

When your done you can just disable it by removing/hash the line out of `/etc/pam.d/su`, no mucking about with packages or ports.