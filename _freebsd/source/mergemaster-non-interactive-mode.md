---
id: 319
title: mergemaster non-interactive mode
date: 2011-11-15T23:34:31+00:00
author: Jake
layout: post
guid: http://www.mebsd.com/?p=319
permalink: /make-build-your-freebsd-word/freebsd-mergemaster-non-interactive-mode.html
parent: Source
grand_parent: freebsd
categories:
  - Build Your FreeBSD World
  - Commands
tags:
  - freebsd 9.0
  - freebsd server
  - freebsd-stable
  - freebsd-update
---
## FreeBSD mergemaster non-interactive mode

Finally all the make buildworld, make buildkernel, make installkernel, reboot, make install world milarky is done, it is now time to run the latest FreeBSD stable. But wait, don't forget the mergemaster!

```sh
# mergemaster -i

*** Creating the temporary root environment in /var/tmp/temproot
 *** /var/tmp/temproot ready for use
 *** Creating and populating directory structure in /var/tmp/temproot

+ ln -s ../var/named/etc/namedb /var/tmp/temproot/etc/namedb
+ ln -s mail/aliases /var/tmp/temproot/etc/aliases

*** Beginning comparison

   *** Checking /etc/rc.d for stale files

   *** No stale files found

   ======================================================================

  *** Displaying differences between ./.cshrc and installed version:

--- /.cshrc     2011-10-18 20:02:06.000000000 +0100
+++ ./.cshrc    2011-11-15 21:10:26.000000000 +0000
@@ -1,4 +1,4 @@
-# $FreeBSD: stable/9/etc/root/dot.cshrc 170088 2007-05-29 06:37:58Z dougb $
+# $FreeBSD: src/etc/root/dot.cshrc,v 1.30.16.1 2011/09/23 00:51:37 kensmith Exp $
 #
 # .cshrc - csh resource script, read at beginning of execution by each shell
 #

  Use 'd' to delete the temporary ./.cshrc
  Use 'i' to install the temporary ./.cshrc
  Use 'm' to merge the temporary and installed versions
  Use 'v' to view the diff results again

  Default is to leave the temporary file to deal with by hand

How should I deal with this? [Leave it for later]
```

Hmmm only the header is changed (as you would expect), I know I've not modified this file, yet I still need to press &#8216;i' and [enter] for each one. This could take some time, but I want to make sure no files I need are overwritten. Do I really have to check each diff?

Nooo! Thank goodness, we have a flag for that `man mergemaster`

```
-U          Attempt to auto upgrade files that have not been user modified.
```

So any file witch I have not messed with will be automatically patched and I can just worry about my random ones, sometimes I still get asked about changes in files that are custom to my build machine, but it's so much better!

```sh
# mergemaster -iU

*** Creating the temporary root environment in /var/tmp/temproot
 *** /var/tmp/temproot ready for use
 *** Creating and populating directory structure in /var/tmp/temproot

+ ln -s ../var/named/etc/namedb /var/tmp/temproot/etc/namedb
+ ln -s mail/aliases /var/tmp/temproot/etc/aliases

*** Beginning comparison

   *** Checking /etc/rc.d for stale files

   *** No stale files found

  *** ./etc/bluetooth/hcsecd.conf has not been user modified.

   *** ./etc/bluetooth/hcsecd.conf upgraded successfully

  *** ./etc/bluetooth/hosts has not been user modified.

   *** ./etc/bluetooth/hosts upgraded successfully

... snip ...

   ======================================================================

  *** Displaying differences between ./etc/mail/freebsd.submit.cf and installed version:

--- /etc/mail/freebsd.submit.cf 2011-10-18 20:02:05.000000000 +0100
+++ ./etc/mail/freebsd.submit.cf        2011-11-15 21:13:53.000000000 +0000
@@ -9,7 +9,7 @@
 # forth in the LICENSE file which can be found at the top level of
 # the sendmail distribution.
 #
-# $FreeBSD: stable/9/contrib/sendmail/cf/m4/cfhead.m4 223067 2011-06-14 04:20:18Z gshapiro $
+# $FreeBSD: src/contrib/sendmail/cf/m4/cfhead.m4,v 1.19.2.1 2011/09/23 00:51:37 kensmith Exp $
 #

 ######################################################################
@@ -17,8 +17,8 @@
 #####
 #####          SENDMAIL CONFIGURATION FILE
 #####
-##### built by root@farrell.cse.buffalo.edu
-##### in /usr/obj/usr/src/etc/sendmail

... etc ...
```