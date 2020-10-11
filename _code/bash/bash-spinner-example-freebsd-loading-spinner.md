---
id: 496
title: 'Bash loading spinner'
date: 2011-12-29T14:36:37+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=496
permalink: /coding-snipits/bash-spinner-example-freebsd-loading-spinner.html
parent: Bash
grand_parent: code
categories:
  - Coding
  - Shell Scripting
tags:
  - bash
  - code
  - shell script
---
# Bash spinner example - FreeBSD loading spinner

As most FreeBSD users have probably noticed, when booting the FreeBSD kernel a crazy spinner animates to let you know the system is doing something. This please wait spinner looks pretty fun and is nice to use for your own scripts that run in the foreground for a while, so the use knows something is happening.

In this post I am going to share a basic way to do this in bash, here is an example that you can copy and paste directly into your bash shell, press enter and the spinner will play.

```sh
i=1; 
sp="/-|"; 
echo -n ' '; 
while true; 
do 
    printf "b${sp:i++%${#sp}:1}"; 
    sleep 0.05; 
done
```

Did you know there is also a FreeBSD port for this style spinner, have a look here `/usr/ports/sysutils/spinner`.

```sh
# cd /usr/ports/sysutils/spinner
# cat pkg-descr
Spinner is a small program that displays a little "spinning" ASCII
character in the top left corner of your terminal. To make this effect
it cycles through punctuation marks like this " -  | / -  | / ... "
(try it to see). By default the character is drawn in inverse video
(or your terminal's equivalent). But you can turn this off with the -i
switch. It supports any terminal capable of handling VT100 style escape codes.

Spinner is useful for keeping telnet and ssh links from dropping due to
inactivity. Many firewalls, and some ISPs drop connections when they are
perceived as idle. By having spinner running the server is constantly
sending a tiny amount of data over the link, preserving the connection.
Thus (for search engines) Spinner is an anti-dle, timeout preventing,
background daemon process for unix variants including linux.

WWW: http://www.laffeycomputer.com/spinner.html

        - Michael L. Hostbaek
        mich@FreeBSD.org
```

Stay tuned for a future post where I will be detailing more shell spinners and progress bars. You can have a lot of fun with these and really add a polish to your scripts, and as described is the pkg-desc above can stop you script from timing out.