---
id: 888
title: Check if service is running on FreeBSD 9.0 breakdown
date: 2012-05-24T21:47:37+01:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=888
permalink: /make-build-your-freebsd-word/check-mysql-running-freebsd-9.html
parent: Ports & Packages
grand_parent: freebsd
categories:
  - Build Your FreeBSD World
  - Commands
  - Configure FreeBSD
  - System Configuration
tags:
  - freebsd 9.0
  - freebsd server
  - rc script
  - service
  - shell script
---
# Check if service is running on FreeBSD 9.0 breakdown

To check if a service is running on FreeBSD the following method can be used.

#### Check if MySQL server is running

```sh
# service mysql-server status
mysql is running as pid 45699.
# ps 45699
  PID  TT  STAT    TIME COMMAND
45699  ??  I    0:00.50 [mysqld]
```

The status can also be checked using the rc.d script directly.

```sh
# /usr/local/etc/rc.d/mysql-server status
mysql is running as pid 45699.
```

For more information on FreeBSD's service rc system, read on!

A simple script to check and ensure all services are running, see <a href="http://www.mebsd.com/make-build-your-freebsd-word/shell-script-freebsd-ensure-all-services-are-running.html" title="Shell Script: FreeBSD ensure all services are running" target="_blank">Shell Script: FreeBSD ensure all services are running</a>

FreeBSD's rc system for starting, stopping or checking the status of daemons, that's like init.d if you're coming from a linux background. The rc files are basically shell scripts that include some generic functions from **/etc/rc.subr**. They can be called directly or if you're running a new enough version, e.g. FreeBSD 9.0, you can use the **service** command.

For services installed with the base system the rc files are stored in the directory **/etc/rc.d** and third party rc files, e.g. installed from ports or packages, are stored in **/usr/local/etc/rc.d**. This is in line with FreeBSD's usual file system structure, files that are part of the base OS are stored below **/usr/local** and third party files are stored in **/usr/local**.

Services ran by the rc system can be enabled or disabled by adding enties into the **/etc/rc.conf** configuration file.

To demonstrate how these commands can be used, we will use mysql-server as an example. All commands should be ran as the root user.

### Usage for rc script

View usage to see all available subcommands, run the rc script with no arguments.

```sh
# /usr/local/etc/rc.d/mysql-server
Usage: /usr/local/etc/rc.d/mysql-server [fast|force|one](start|stop|restart|rcvar|status|poll)
```

### Enable the service

First, to enable a service we would add an entry into **/etc/rc.conf**, so first we need to find out what this entry should be. The rc system provides an **rcvar** subcommand which will tell us what we should be using.

#### Find the rcvar for /etc/rc.conf

```sh
# /usr/local/etc/rc.d/mysql-server rcvar
# mysql
#
mysql_enable="YES"
#   (default: "")
```

So now we know the line we need to add into `/etc/rc.conf`, `mysql\_enable="YES"`, and to disable the rc script for `mysql-server` we would simply change this to `mysql\_enable="NO"`, removing the line would also have the same effect.

The rc.conf file can get messy so it's a good idea to comment it, rcvar already prints the basic comments we need, so I would recommend enabling the service like this, instead of editing the file manually.

```sh
# /usr/local/etc/rc.d/mysql-server rcvar >> /etc/rc.conf
```

If you attempt to start a service without adding the `rcvar` to `/etc/rc.conf` you will get the error below. As the error says if we want to start the service anyway we just need to append 'one' to the subcommand, e.g. onestart or onestop, but remember the service will not start on boot without an `rc.conf` entry.

```sh
# /usr/local/etc/rc.d/mysql-server start
Cannot 'start' mysql. Set mysql_enable to YES in /etc/rc.conf or use 'onestart' instead of 'start'.
```

N.B. You can also append 'force' to a subcommand, e.g. 'forcestart' or 'forcestop'.

So far we have been calling the rc file directly, by using **/usr/local/etc/rc.d/mysql-server**. These days you can use the **service** command in FreeBSD instead, just like in linux. The **service** command and the full path to the rc file are interchangeable, anything you can do with one you can do with the other, with the service command you can do a bit more. From here on we will be using the **service** command in our examples.

### Start the service

Now mysql-server is enabled in `/etc/rc.conf` we can start it, here's how.

```sh
# service mysql-server start
Starting mysql.
```

### Check if the service is running

To check if the service is running we use the **status** subcommand.

```sh
# service mysql-server status
mysql is running as pid 44665.
# echo $?
0
```

Status will print a message to stdout with the pid of the running process, it also returns an exit code which can be checked by accessing the special variable '$?'. This variable stores the exit code of the last executed command. Exit codes are very useful to quicky check how a command finished executing, if you are checking a service from a shell script exit codes are very programmatically friendly.

### Polling a service

Another method to check if a service is running is the poll subcommand. Poll will keep checking (polling) the pid of a running service indefinitely until canceled or the service dies and the pid is removed.

```sh
# service mysql-server poll
Waiting for PIDS: 45178.
```

It should be noted that if the service is not running, poll with return nothing. Perhaps unexpectedly returns an exit status of '0'. Watch out for this if using poll in scripts.

_**If you are still reading at this point you have probably realised how simple the service rc system is to use, so the follwing examples should come as no supprise.**_

### Stop a running service

```sh
# service mysql-server stop
Stopping mysql.
Waiting for PIDS: 44665.
```

Now if we check the status of the service as we did before we would see something like this.

```sh
# service mysql-server status
mysql is not running.
# echo $?
1
```

Note how the exit code now shows as '1', meaning something is wrong.

### Restart a service

```sh
# service mysql-server restart
mysql not running? (check /var/db/mysql/server.example.com.pid).
Starting mysql.
```

### The 'fast' subcommand

The only subcommand we have not covered yet is the 'fast' command, e.g. faststart. When starting a service normally rc will first check if the service is already running, it does this by looking for the pid file. If it find a pid it wont try and start it again, but by using faststart you can skip this check and attempt to start the service anyway.

```sh
# service mysql-server start
mysql already running? (pid=45699).
# echo $?
1
# service mysql-server faststart
Starting mysql.
# echo $?
0
```

### More about the 'service' command

The service command has a few extra options that can be handy when administrating services.

```sh
# service -h

Usage:
service -e
service [-v] -l | -r
service [-v] <rc.d script> start|stop|etc.
service -h

-e      Show services that are enabled
-l      List all scripts in /etc/rc.d and /usr/local/etc/rc.d
-r      Show the results of boot time rcorder
-v      Verbose
```

This is an extract from the <a href="http://www.mebsd.com/man/service/8" title="service(8) man page" target="_blank">service man page, service(8) (man service)</a>, explaining these additonal flags.

```sh
      -e  List services that are enabled.  The list of scripts to check is com-
         piled using rcorder(8) the same way that it is done in rc(8), then
         that list of scripts is checked for an "rcvar" assignment.  If
         present the script is checked to see if it is enabled.

     -l  List all files in /etc/rc.d and the local startup directories.  As
         described in rc.conf(5) this is usually /usr/local/etc/rc.d.  All
         files will be listed whether they are an actual rc.d script or not.

     -r  Generate the rcorder(8) as in -e above, but list all of the files,
         not just what is enabled.

     -v  Be slightly more verbose
```

#### Show services that are enabled

```sh
# service -e
/etc/rc.d/hostid
/etc/rc.d/zvol
/etc/rc.d/hostid_save
/etc/rc.d/zfs
/etc/rc.d/ip6addrctl
/etc/rc.d/cleanvar
/etc/rc.d/devd
/etc/rc.d/ipfw
/etc/rc.d/newsyslog
/etc/rc.d/syslogd
/etc/rc.d/ntpdate
/etc/rc.d/dmesg
/etc/rc.d/virecover
/etc/rc.d/motd
/etc/rc.d/ntpd
/usr/local/etc/rc.d/mysql-server
/etc/rc.d/sshd
/etc/rc.d/sendmail
/etc/rc.d/cron
/etc/rc.d/mixer
/etc/rc.d/gptboot
/etc/rc.d/bgfsck
```

#### List all scripts in /etc/rc.d and /usr/local/etc/rc.d

I have snipped the output as this list is quiet long.

```sh
# service -l
DAEMON
FILESYSTEMS
LOGIN
NETWORKING
SERVERS
abi
accounting
addswap
adjkerntz
amd
apm
apmd
archdep
atm1
atm2
atm3
auditd
bgfsck
--- SNIP ---
```

#### Show the results of boot time rcorder

In this one we have added the -v (verbose) flag to show the early/late divide, scripts which are ran early on during boot. This output is also snipped.

```sh
# service -rv
rc.d/sysctl
/etc/rc.d/hostid
/etc/rc.d/zvol
/etc/rc.d/dumpon
/etc/rc.d/ddb
/etc/rc.d/initrandom
/etc/rc.d/geli
/etc/rc.d/gbde
/etc/rc.d/encswap
/etc/rc.d/ccd
/etc/rc.d/swap1
/etc/rc.d/fsck
/etc/rc.d/root
/etc/rc.d/mdconfig
/etc/rc.d/hostid_save
/etc/rc.d/mountcritlocal
/etc/rc.d/zfs
/etc/rc.d/FILESYSTEMS
========= Early/Late Divider =========
/etc/rc.d/kld
/etc/rc.d/var
/etc/rc.d/random
/etc/rc.d/adjkerntz
/etc/rc.d/atm1
/etc/rc.d/hostname
--- SNIP ---
```

### Service tab completion &#8211; bash

If you use a bash shell you can enable tab completion for the service command. The man page for service gives an example function for doing this. Thanks to Hurle for the comment pointing this out!

First create a script containing the programmable completion function.

#### File: complete_service.sh

```sh
_service () {
     local cur
     cur=${COMP_WORDS[COMP_CWORD]}
     COMPREPLY=( $( compgen -W '$( service -l )' -- $cur ) )
     return 0
}
complete -F _service service
```

#### Source the file

```sh
# source complete_service.sh
```

To run this on login simply add the source command to your **~/.bash_profile**.

#### Show all available rc scripts.

Now you can tab complete the rc script name. `[TAB]` means, press the tab key.

```sh
# service [TAB][TAB]
```

#### Complete mysql-server service rc name.

```sh
# service my[TAB]
```

For more information on bash programmable completion there is a very good write up here <a href="http://tldp.org/LDP/abs/html/tabexpansion.html" title="Appendix I. An Introduction to Programmable Completion" target="_blank">http://tldp.org/LDP/abs/html/tabexpansion.html</a>.