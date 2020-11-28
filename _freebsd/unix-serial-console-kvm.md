---
title: 'FreeBSD: IPKVM with a RS232 serial cable'
date: 2012-06-29T14:03:10+01:00
author: Jake
layout: default
permalink: /cheat-sheets/unix-serial-console-kvm.html
categories:
  - Cheat Sheets
  - Configure FreeBSD
  - System Configuration
tags:
  - freebsd 9.0
  - freebsd 9.0-RELEASE
  - freebsd server
---
## FreeBSD: IPKVM with a RS232 serial cable

One of the advantages of running a Unix operating system, such as FreeBSD, is the ability to setup a quick and cheap IPKVM between 2 servers with nothing more than a 9 pin RS232 DE9 serial cable. This post describes how to configure console redirection to a serial port providing access to the BIOS, boot menu, kernel, single and multi user boot levels.

### 1. Physical connection between servers.

The first thing we need to do is connect the servers to each other using a female to female DE9 serial cable. I had a few of these sitting in a box, but if you need to buy one they cost very little, here is one from amazon <a title="Serial RS232 9 pin Connection Cable" href="http://www.amazon.co.uk/Serial-RS232-Connection-Cable-Female/dp/B00228GQCY" target="_blank">Serial RS232 9 pin Cable</a>.

### 2. Enable console redirection in BIOS (optional)

To enable access to the BIOS via the serial cable you need to go into the BIOS to enable it. Although slower I would recommend setting the braud rate to 9600 as FreeBSD uses this by default. It's possible you BIOS might not support this option, and this step is not required. You can still access FreeBSD via serial console without this step.

#### BIOS screen from minicom

```sh
                    Phoenix cME FirstBIOS Pro Setup Utility
             Advanced
uXE 2.1 Build 084 (WfM 2.0)
#               Console Redirection                  #   Item Specific Help    #
# > Boot Features                                    #
#                                                    #                         #
#   Com Port Address           [On-board COM A]      # If enabled, it will     #
#                                                    # use a port on the       #
#   Baud Rate                  [9600]                # motherboard.            #
#   Console Type               [PC ANSI]             #                         #
#   Flow Control               [CTS/RTS]             #                         #
#   Console connection:        [Direct]              #                         #
#   Continue C.R. after POST:  [Off]                 #                         #
#                                                    #                         #
#                                                    #                         #
#                                                    #                         #
#                                                    #                         #
#                                                    #                         #
#                                                    #                         #
#                                                    #                         #
#                                                    #                         #
#                                                    #                         #
##
   F1   Help  ^v  Select Item  -/+    Change Values      F9   Setup Defaults
   Esc  Exit  <   Select Menu  Enter  Select > Sub-Menu  F10  Save and Exit
```

### 3. Enable console redirection for kernel

To access the boot menu, single user mode and see the boot messages you need to enable console redirection in `/boot/loader.conf`.

#### File: /boot/loader.conf

```sh
console="comconsole,vidconsole"
```

The value comconsole redirects the console to the serial port, vidconsole redirects the console the the actuall video monitor. If you don't include vidconsole you will see no output on your video monitor on boot.

If you want to change the braud rate speed see this page. <a title="Setting a Faster Serial Port Speed" href="http://www.freebsd.org/doc/en_US.ISO8859-1/books/handbook/serialconsole-setup.html#AEN33771" target="_blank">http://www.freebsd.org/doc/en_US.ISO8859-1/books/handbook/serialconsole-setup.html#AEN33771</a> from the FreeBSD documentation. Personally I didn't have any success changing the speed with the comconsole_speed option, it would remain at 9600. Adding the option, options CONSPEED=19200, to the kernel and recompiling would probably do the trick, but I did not test this.

You will now be able to access the FreeBSD boot menu and watch the kenel boot via the serial console.

```sh
  ______               ____   _____ _____
 |  ____|             |  _  / ____|  __ 
 | |___ _ __ ___  ___ | |_) | (___ | |  | |
 |  ___| '__/ _ / _ |  _ < ___ | |  | |
 | |   | | |  __/  __/| |_) |____) | |__| |
 | |   | | |    |    ||     |      |      |
 |_|   |_|  ___|___||____/|_____/|_____/    ```                        `
                                             s` `.....---.......--.```   -/
 #############Welcome to FreeBSD############ +o   .--`         /y:`      +.
 #                                         #  yo`:.            :o      `+-
 #  1. Boot [ENTER]                        #   y/               -/`   -o/
 #  2. [Esc]ape to loader prompt           #  .-                  ::/sy+:.
 #  3. Reboot                              #  /                     `--  /
 #                                         # `:                          :`
 #  Options:                               # `:                          :`
 #  4. [A]CPI Support: Enabled             #  /                          /
 #  5. Boot Safe [M]ode: NO                #  .-                        -.
 #  6. Boot [S]ingle User: NO              #   --                      -.
 #  7. Boot [V]erbose: NO                  #    `:`                  `:`
 #                                         #      .--             `--.
 #                                         #         .---.....----.
 ###########################################

Copyright (c) 1992-2012 The FreeBSD Project.
Copyright (c) 1979, 1980, 1983, 1986, 1988, 1989, 1991, 1992, 1993, 1994
        The Regents of the University of California. All rights reserved.
FreeBSD is a registered trademark of The FreeBSD Foundation.
FreeBSD 9.0-RELEASE #0: Tue Jan  3 07:46:30 UTC 2012
    root@farrell.cse.buffalo.edu:/usr/obj/usr/src/sys/GENERIC amd64
```

### 4. Enable console redirection for the TTYs

To login and access the command line once the kernel hands over the the userland and starts the TTYs you need to modify `/etc/ttys`.

#### file: /etc/ttys

Find the following section and update the type to vt100 and status to on. Be carful here, if you don't set the type correctly you may be able to access the server with a login prompt!

```sh
# Serial terminals
# The 'dialup' keyword identifies dialin lines to login, fingerd etc.
ttyu0   "/usr/libexec/getty std.9600"   vt100   on secure
ttyu1   "/usr/libexec/getty std.9600"   vt100   on secure
ttyu2	"/usr/libexec/getty std.9600"	dialup	off secure
ttyu3	"/usr/libexec/getty std.9600"	dialup	off secure
```

Now you will have full shell access via a serial console.

```sh
FreeBSD/amd64 (freebsd9) (ttyu0)

login: root
Password:
Last login: Fri Jun 29 14:16:48 on ttyu0
FreeBSD 9.0-RELEASE (GENERIC) #0: Tue Jan  3 07:46:30 UTC 2012

Welcome to FreeBSD!

Before seeking technical support, please use the following resources:

o  Security advisories and updated errata information for all releases are
   at http://www.FreeBSD.org/releases/ - always consult the ERRATA section
   for your release first as it's updated frequently.

o  The Handbook and FAQ documents are at http://www.FreeBSD.org/ and,
   along with the mailing lists, can be searched by going to
   http://www.FreeBSD.org/search/.  If the doc package has been installed
   (or fetched via pkg_add -r lang-freebsd-doc, where lang is the
   2-letter language code, e.g. en), they are also available formatted
   in /usr/local/share/doc/freebsd.

If you still have a question or problem, please take the output of
`uname -a', along with any relevant error messages, and email it
as a question to the questions@FreeBSD.org mailing list.  If you are
unfamiliar with FreeBSD's directory layout, please refer to the hier(7)
manual page.  If you are not familiar with manual pages, type `man man'.

Edit /etc/motd to change this login announcement.

You have new mail.
#
```

### 5. Connect to the serial port

To communicate with the serial port there are a few options, the easiest thing to do on FreeBSD is use `cu`, which is in the base and works very well. You can also use `screen` or `minicom`, below is how you can setup minicom.

#### Install minicom

```sh
# cd /usr/ports/comms/minicom
# make install
```

Once installed run **minicom -s** to setup the initial configuration.

```sh
# minicom -s

            ┌─────[configuration]──────┐
            │ Filenames and paths      │
            │ File transfer protocols  │
            │ Serial port setup        │
            │ Modem and dialing        │
            │ Screen and keyboard      │
            │ Save setup as dfl        │
            │ Save setup as..          │
            │ Exit                     │
            │ Exit from Minicom        │
            └──────────────────────────┘
```

Select **Serial port setup** and then press `E` to change the serial speed, set it to 9600. You may also need to change the **Serial Device** to match your serial port device.

```sh
┌───────────────────────────────────────────────────────────────────────┐
    │ A -    Serial Device      : /dev/cuau0                                │
    │ B - Lockfile Location     : /var/spool/lock                           │
    │ C -   Callin Program      :                                           │
    │ D -  Callout Program      :                                           │
    │ E -    Bps/Par/Bits       : 9600 8N1                                  │
    │ F - Hardware Flow Control : Yes                                       │
    │ G - Software Flow Control : No                                        │
    │                                                                       │
    │    Change which setting?                                              │
    └───────────────────────────────────────────────────────────────────────┘
            │ Screen and keyboard      │
            │ Save setup as dfl        │
            │ Save setup as..          │
            │ Exit                     │
            │ Exit from Minicom        │
            └──────────────────────────┘
```

A good way to test all the stages are setup correcty, is connect to the server via minicom and issue a reboot. If you see all the boot stages!