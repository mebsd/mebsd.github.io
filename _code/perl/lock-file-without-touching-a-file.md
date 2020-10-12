---
id: 327
title: 'Perl lock file - without touching a file'
date: 2011-11-16T13:37:12+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=327
permalink: /coding-snipits/perl-lock-file-without-touching-a-file.html
parent: Perl
grand_parent: code
categories:
  - Coding
  - Perl Programming
tags:
  - code
  - perl examples
  - programming
  - shell script
---
# Perl lock file - without touching a file

If you have any scripts in your crontab that are run regularly but sometimes run for too long and end up running twice you need something like a lock file/pid file. This can get messy if scripts crash, servers reboot, etc and the lock file doesn't get remove and - oh no - the script hasn't been running all day...

Another solution to locking a file from being executed more than once in perl is like this. 

```perl
#!/usr/bin/perl

use Fcntl ':flock'; # import LOCK_* constants

INIT{
  open  *{0}
        or die "What!? $0:$!";
  flock *{0}, LOCK_EX|LOCK_NB
    or die "$0 is already running somewhere!n";
}
```

Give it a try :)