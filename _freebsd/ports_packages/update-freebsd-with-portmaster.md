---
title: Update FreeBSD and Ports with portmaster
author: Jake
layout: default
parent: Ports & Packages
grand_parent: FreeBSD
permalink: /freebsd-security-hardening/update-freebsd-with-portmaster.html
categories:
  - BSD Hardening
  - Build Your FreeBSD World
  - Configure FreeBSD
  - System Configuration
tags:
  - freebsd server
  - freebsd update script
  - freebsd-update
  - upgrade openssl package
---
# Update FreeBSD and Ports with portmaster

Recent talks on FreeBSD-Ports lists have been about whether [portupgrade](http://www.mebsd.com/man/portupgrade/1 "man portupgrade") should be shown the door, [portmaster](http://www.mebsd.com/man/portmaster/8 "man portmaster") seems to be the new way to go.

<http://lists.freebsd.org/pipermail/freebsd-ports/2011-July/068887.html>

I still quite like [portupgrade](http://www.mebsd.com/man/portupgrade/1 "man portupgrade") for some reasons but I would rather play it safe and jump on to [portmaster](http://www.mebsd.com/man/portmaster/8 "man portmaster") before [portupgrade](http://www.mebsd.com/man/portupgrade/1 "man portupgrade") sinks. In an old post I gave an example shell script that would run and bunch of stuff to update FreeBSD, in that script I used [portupgade](http://www.mebsd.com/man/portupgrade/1 "man portupgrade"), this is the revised script using [portmaster](http://www.mebsd.com/man/portmaster/8 "man portmaster").

First make sure you have the following installed

  * [freebsd-update](http://www.mebsd.com/man/freebsd-update/8 "man freebsd-update") (this is installed as default)
  * [portsnap](http://www.mebsd.com/man/portsnap/8 "man portsnap") (should also be installed as default)
  * [portmaster](http://www.mebsd.com/man/portmaster/8 "man portmaster") (/usr/ports/ports-mgmt/portmaster)
  * [portaudit](http://www.mebsd.com/man/portaudit/1 "man portaudit") (/usr/ports/ports-mgmt/portaudit)

```sh
#!/bin/sh

LOG_FILE="/var/log/freebsd-update.log"

echo "Starting updates: `date`" | tee -a ${LOG_FILE}
echo "***"
echo "*** Checking for FreeBSD patches..."
echo "***"
/usr/sbin/freebsd-update fetch | tee -a ${LOG_FILE}
/usr/sbin/freebsd-update install | tee -a ${LOG_FILE}

echo "***"
echo "*** Updating ports tree..."
echo "***"
/usr/sbin/portsnap fetch update | tee -a ${LOG_FILE}

echo "***"
echo "*** Looking for ports to update..."
echo "***"
/usr/local/sbin/portmaster -a --no-confirm | tee -a ${LOG_FILE}

echo "***"
echo "*** Checking installed ports for known security problems..."
echo "***"
/usr/local/sbin/portaudit -Fva | tee -a ${LOG_FILE}
echo "Finished updates: `date`" | tee -a ${LOG_FILE}
```