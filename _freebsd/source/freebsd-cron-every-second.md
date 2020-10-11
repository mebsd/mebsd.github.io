---
title: 'Crontab run every second now in HEAD'
author: Jake
layout: default
parent: Source
grand_parent: freebsd
permalink: /make-build-your-freebsd-word/freebsd-cron-every-second.html
categories:
  - Build Your FreeBSD World
  - Configure FreeBSD
tags:
  - cron
  - crontab
  - freebsd current
  - head
---
## Crontab run every second now in HEAD
Last night a new functionality and cron shortcut was added to the FreeBSD crontab in HEAD, `@every_second`.

It does what it says on the tin, runs a cron entry once a second. A feature which I&#8217;m sure will be welcomed by many. Currently with the latest commit you can only run once a second, no support for things like every 10 seconds, every 30 seconds, etc but I with any luck this functionality will be added soon.

#### See sobomax&#8217;s commit log

```
r241576 | sobomax | 2012-10-15 09:21:49 +0100 (Mon, 15 Oct 2012) | 16 lines
Changed paths:
   M /head/usr.sbin/cron/cron/cron.c
   M /head/usr.sbin/cron/cron/cron.h
   M /head/usr.sbin/cron/crontab/crontab.5
   M /head/usr.sbin/cron/lib/entry.c

Add per-second scheduling into the cron(8). Right now it's
only available via the new @every_second shortcut. ENOTIME to
implement crontab(5) format extensions to allow more flexible
scheduling.

In order to address some concerns expressed by Terry Lambert
while discussing the topic few years ago, about per-second cron
possibly causing some bad effects on /etc/crontab by stat()ing
it every second instead of every minute now (i.e. atime update),
only check that database needs to be reloaded on every 60-th
loop run. This should be close enough to the current behaviour.

Add "@every_minute" shortcut while I am here.

MFC after:	1 month
```

Thanks to everyone involved! As you can see from the commit log `@every_minute` is now also available.

#### Example crontab entry

```
@every_second        root        echo "hello, world"
```

Initial tests are working well, if you want to give it a go check out the latest HEAD from svn, build and get testing!