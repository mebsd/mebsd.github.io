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
## Update FreeBSD and Ports with portmaster

First make sure you have the following installed

  * `freebsd-update` (this is installed as default)
  * `portsnap` (should also be installed as default)
  * `portmaster` (/usr/ports/ports-mgmt/portmaster)
  * `portaudit` (/usr/ports/ports-mgmt/portaudit)

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