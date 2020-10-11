---
title: FreeBSD package update script
author: Jake
layout: default
parent: Ports & Packages
grand_parent: freebsd
permalink: /freebsd-security-hardening/simple-script-for-keeping-freebsd-upto-date.html
categories:
  - BSD Hardening
  - Build Your FreeBSD World
  - Configure FreeBSD
  - System Configuration
tags:
  - code
  - freebsd server
  - freebsd update script
  - freebsd-update
  - shell script
  - upgrade openssl package
---
## Script to update FreeBSD packages
 
The script first updates to the latest binary patch from FreeBSD, then update the ports tree and upgrade any outdated ports including their dependencies. Finally it fetches the latest portaudit database and checks your installed packages for known vulnerabilities. The output is also duplicated to a log file if you need it.

The following packages are required:

  * freebsd-update (this is installed as default)
  * portsnap (should also be installed as default)
  * portupgrade (/usr/ports/ports-mgmt/portupgrade)
  * portaudit (/usr/ports/ports-mgmt/portaudit)

Now you have everything you need to update your server, this script will also create backup of the old port in case anything goes wrong. See `man 1 portupgrade` for more information on backing up packages.

One other thing worth mentioning is that `portupgrade` expects your packages to be fairly conflict free and tidy. Although this script will help keep your `pkgdb` that way you may find that you need to manually intervene sometimes to fix conflicts. You do this with this command

```sh
# pkgdb -F
```


The script:
```sh
#!/bin/sh

LOG_FILE="/var/log/freebsd-update.log"

echo "Starting updates: `date`" | tee ${LOG_FILE}
echo "***"
echo "*** Checking for FreeBSD patches..."
echo "***"
/usr/sbin/freebsd-update fetch | tee ${LOG_FILE}
/usr/sbin/freebsd-update install | tee ${LOG_FILE}

echo "***"
echo "*** Updating ports tree..."
echo "***"
/usr/sbin/portsnap fetch update | tee ${LOG_FILE}

echo "***"
echo "*** Checking pkgdb..."
echo "***"
/usr/local/sbin/pkgdb -aFv | tee ${LOG_FILE}

echo "***"
echo "*** Looking for ports to update..."
echo "***"
/usr/local/sbin/portversion -v -l '<' | tee ${LOG_FILE}
/usr/local/sbin/portupgrade -aRrbv --batch | tee ${LOG_FILE}
/usr/local/sbin/portversion -v | tee ${LOG_FILE}

echo "***"
echo "*** Checking installed ports for known security problems..."
echo "***"
/usr/local/sbin/portaudit -Fva | tee ${LOG_FILE}
echo "Finished updates: `date`" | tee ${LOG_FILE}
```
