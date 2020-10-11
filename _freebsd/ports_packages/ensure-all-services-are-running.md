---
id: 909
title: 'Ensure all services are running'
date: 2012-05-27T15:57:37+01:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=909
permalink: /make-build-your-freebsd-word/shell-script-freebsd-ensure-all-services-are-running.html
parent: Ports & Packages
grand_parent: freebsd
image: /wp-content/uploads/2012/05/service.png
categories:
  - Build Your FreeBSD World
  - Coding
  - Shell Scripting
tags:
  - freebsd 9.0
  - rc script
  - shell script
---
# Shell Script: FreeBSD ensure all services are running

The FreeBSD rc system makes it very simple to check and control services. With the addition of the `service` command you can easily list enabled services. This script ensures all services are running. It uses `service -e` to get enabled services, if rc status reports service is not running, start it.

I don't know of any built in functionality to do this, however writing a script to do it is pretty simple. My script below uses the `service -e` command to get the list of enabled services. It then uses the `status` subcommand to check if the service is running, if it is not, the service will be started.

Not all rc scripts start a daemon, or by design will not run continuously. An example of this is background fsck. To stop these from being started and to give the user granulated control, the script supports an ignore list.

#### Check if services are running, if not, start them

```sh
#!/bin/sh
#
# Script to check if enabled rc scripts are running, if not start them
#
# Uses ``<rc.d script> status'' to check if service is running.
# Some rc scripts do not run a simple daemon, we also might not want
# to check them, add the rc script name to IGNORE_LIST.
#
# Jake Smith, <jake at xz.cx>
# http://mebsd.com

# Ignore list, services we don't want to start, e.g. bgfsck, newsyslog.
# Note space at ends of string and in test var, this ensures exact matches.
IGNORE_LIST=" bgfsck newsyslog ntpdate "

# Get list of enabled rc scripts
/usr/sbin/service -e | while read SERVICE
do
    # is rc script in ignore list?
    test "${IGNORE_LIST#* $(basename ${SERVICE}) }" != "${IGNORE_LIST}" && continue

    # check rc script supports status
    ${SERVICE} 2>&1 | /usr/bin/grep '|status|poll' >/dev/null
    if [ $? -eq 0 ]
    then
        # check status
        STATUS=$(${SERVICE} status)
        if [ $? -gt 0 ]
        then
            # service not running try to start
            echo ${STATUS}
            ${SERVICE} start
            ${SERVICE} status
        fi
    fi
done
```