---
title: '102 Tips and Tricks'
date: 2012-10-03T08:28:53+01:00
permalink: /cheat-sheets/102-freebsd-tips-trick.html
layout: default
nav_order: 1
categories:
  - Cheat Sheets
tags:
  - Cheat Sheets
  - freebsd server
  - tips
---
These tips will look familiar to any one who has games/fortune in their MOTD, because indeed this is where they&#8217;re from. These tips are well worth reading, even for an experienced FreeBSD user. For example, how many users know about the &#8220;look&#8221; command, I for one am enlightened!

#### FreeBSD Tip: #1

```
Any user that is a member of the wheel group can use "su -" to simulate
a root login. You can add a user to the wheel group by editing /etc/group.
		-- Konstantinos Konstantinidis <kkonstan at duth.gr>
```

#### FreeBSD Tip: #2

```
By pressing "Scroll Lock" you can use the arrow keys to scroll backward
through the console output.  Press "Scroll Lock" again to turn it off.
```

#### FreeBSD Tip: #3

```
Can't remember if you've installed a certain port or not? Try "pkg_info
-Ix port_name".
```

#### FreeBSD Tip: #4

```
Ever wonder what those numbers after command names were, as in cat(1)?  It's
the section of the manual the man page is in.  "man man" will tell you more.
		-- David Scheidt <dscheidt at tumbolia.com>
```

#### FreeBSD Tip: #5

```
Forget how to spell a word or a variation of a word? Use

	look portion_of_word_you_know
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #6

```
Forget what directory you are in? Type "pwd".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #7

```
Forget when Easter is? Try "ncal -e". If you need the date for Orthodox
Easter, use "ncal -o" instead.
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #8

```
FreeBSD is started up by the program 'init'.  The first thing init does when
starting multiuser mode (ie, starting the computer up for normal use) is to
run the shell script /etc/rc.  By reading /etc/rc and the /etc/rc.d/ scripts,
you can learn a lot about how the system is put together, which again will
make you more confident about what happens when you do something with it.
```

#### FreeBSD Tip: #9

```
Handy bash(1) prompt:  PS1="u at h w !$ "
		-- David Scheidt <dscheidt at tumbolia.com>
```

#### FreeBSD Tip: #10

```
Having trouble using fetch through a firewall? Try setting the environment
variable FTP_PASSIVE_MODE to yes, and see fetch(3) for more details.
```

#### FreeBSD Tip: #11

```
If other operating systems have damaged your Master Boot Record, you can
reinstall it either with /usr/sbin/sysinstall or with boot0cfg(8). See
"man boot0cfg" for details.
```

#### FreeBSD Tip: #12

```
If you accidentally end up inside vi, you can quit it by pressing Escape, colon
(:), q (q), bang (!) and pressing return.
```

#### FreeBSD Tip: #13

```
If you are in the C shell and have just installed a new program, you won't
be able to run it unless you first type "rehash".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #14

```
If you do not want to get beeps in X11 (X Windows), you can turn them off with

	xset b off
```

#### FreeBSD Tip: #15

```
If you have a CD-ROM drive in your machine, you can make the CD-ROM that is
presently inserted available by typing 'mount /cdrom' as root.  The CD-ROM
will be available under /cdrom/.  Remember to do 'umount /cdrom' before
removing the CD-ROM (it will usually not be possible to remove the CD-ROM
without doing this.)

Note: This tip may not work in all configurations.
```

#### FreeBSD Tip: #16

```
If you need a reminder to leave your terminal, type "leave +hhmm" where
"hhmm" represents in how many hours and minutes you need to leave.
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #17

```
If you need to ask a question on the FreeBSD-questions mailing list then

	http://www.FreeBSD.org/doc/en_US.ISO8859-1/articles/
		freebsd-questions/index.html

contains lots of useful advice to help you get the best results.
```

#### FreeBSD Tip: #18

```
If you write part of a filename in tcsh,
pressing TAB will show you the available choices when there
is more than one, or complete the filename if there's only one match.
```

#### FreeBSD Tip: #19

```
If you `set watch = (0 any any)' in tcsh, you will be notified when
someone logs in or out of your system.
```

#### FreeBSD Tip: #20

```
If you use the C shell, add the following line to the .cshrc file in your
home directory to prevent core files from being written to disk:

	limit coredumpsize 0
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #21

```
If you want df(1) and other commands to display disk sizes in
kilobytes instead of 512-byte blocks, set BLOCKSIZE in your
environment to 'K'.  You can also use 'M' for Megabytes or 'G' for
Gigabytes.  If you want df(1) to automatically select the best size
then use 'df -h'.
```

#### FreeBSD Tip: #22

```
If you want to play CDs with FreeBSD, a utility for this is already included.
Type 'cdcontrol' then 'help' to learn more.  (You may need to set the CDROM
environment variable in order to make cdcontrol want to start.)
```

#### FreeBSD Tip: #23

```
If you want to quickly check for duplicate package/port installations,
try the following pkg_info command.

	pkg_info | sort | sed -e 's/-[0-9].*$//' | 
	uniq -c | grep -v '^[[:space:]]*1'
```

#### FreeBSD Tip: #24

```
If you'd like to keep track of applications in the FreeBSD ports tree, take a
look at FreshPorts;

	http://www.freshports.org/
```

#### FreeBSD Tip: #25

```
In order to make fetch (the FreeBSD downloading tool) ask for
username/password when it encounters a password-protected web page, you can set
the environment variable HTTP_AUTH to 'basic:*'.
```

#### FreeBSD Tip: #26

```
In order to search for a string in some files, use 'grep' like this:

	 grep "string" filename1 [filename2 filename3 ...]

This will print out the lines in the files that contain the string.  grep can
also do a lot more advanced searches - type 'man grep' for details.
```

#### FreeBSD Tip: #27

```
In order to support national characters for European languages in tools like
less without creating other nationalisation aspects, set the environment
variable LC_ALL to 'en_US.ISO8859-1'.
```

#### FreeBSD Tip: #28

```
"man firewall" will give advice for building a FreeBSD firewall
		-- David Scheidt <dscheidt at tumbolia.com>
```

#### FreeBSD Tip: #29

```
"man hier" will explain the way FreeBSD filesystems are normally laid out.
		-- David Scheidt <dscheidt at tumbolia.com>
```

#### FreeBSD Tip: #30

```
Man pages are divided into section depending on topic.  There are 9 different
sections numbered from 1 (General Commands) to 9 (Kernel Developer's Manual).
You can get an introduction to each topic by typing

	man <number> intro

In other words, to get the intro to general commands, type

	man 1 intro
```

<!--nextpage-->

#### FreeBSD Tip: #31

```
"man ports" gives many useful hints about installing FreeBSD ports.
```

#### FreeBSD Tip: #32

```
"man security" gives very good advice on how to tune the security of your
FreeBSD system.
```

#### FreeBSD Tip: #33

```
"man tuning" gives some tips how to tune performance of your FreeBSD system.
		-- David Scheidt <dscheidt at tumbolia.com>
```

#### FreeBSD Tip: #34

```
Need to do a search in a manpage or in a file you've sent to a pager? Use
"/search_word". To repeat the same search, type "n" for next.
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #35

```
Need to find the location of a program? Use "locate program_name".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #36

```
Need to leave your terminal for a few minutes and don't want to logout?
Use "lock -p". When you return, use your password as the key to unlock the
terminal.
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #37

```
Need to print a manpage? Use

	man name_of_manpage | col -bx | lpr
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #38

```
Need to quickly empty a file? Use ": > filename".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #39

```
Need to quickly return to your home directory? Type "cd".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #40

```
Need to remove all those ^M characters from a DOS file? Try

	tr -d \r < dosfile > newfile
		-- Originally by Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #41

```
Need to see the calendar for this month? Simply type "cal".  To see the
whole year, type "cal -y".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #42

```
Need to see which daemons are listening for connection requests? Use
"sockstat -4l" for IPv4, and "sockstat -l" for IPv4 and IPv6.
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #43

```
Need to see your routing table? Type "netstat -rn". The entry with the G
flag is your gateway.
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #44

```
Nice bash prompt: PS1='([$(tput md)]t <w>[$(tput me)]) $(echo $?) $ '
		-- Mathieu <mathieu at hal.interactionvirtuelle.com>
```

#### FreeBSD Tip: #45

```
Over quota?  "du -s * | sort -n " will give you a sorted list of your
directory sizes.
		-- David Scheidt <dscheidt at tumbolia.com>
```

#### FreeBSD Tip: #46

```
nc(1) (or netcat) is useful not only for redirecting input/output to
TCP or UDP connections, but also for proxying them with inetd(8).
```

#### FreeBSD Tip: #47

```
sh (the default Bourne shell in FreeBSD) supports command-line editing.  Just
``set -o emacs'' or ``set -o vi'' to enable it.
```

#### FreeBSD Tip: #48

```
Simple tcsh prompt: set prompt = '%# '
```

#### FreeBSD Tip: #49

```
The default editor in FreeBSD is vi, which is efficient to use when you have
learned it, but somewhat user-unfriendly.  To use ee (an easier but less
powerful editor) instead, set the environment variable EDITOR to /usr/bin/ee
```

#### FreeBSD Tip: #50

```
Time to change your password? Type "passwd" and follow the prompts.
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #51

```
To change an environment variable in /bin/sh use:

	$ VARIABLE="value"
	$ export VARIABLE
```

#### FreeBSD Tip: #52

```
To change an environment variable in tcsh you use: setenv NAME "value"
where NAME is the name of the variable and "value" its new value.
```

#### FreeBSD Tip: #53

```
To clear the screen, use "clear". To re-display your screen buffer, press
the scroll lock key and use your page up button. When you're finished,
press the scroll lock key again to get your prompt back.
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #54

```
To determine whether a file is a text file, executable, or some other type
of file, use

	file filename
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #55

```
To do a fast search for a file, try

	 locate filename

locate uses a database that is updated every Saturday (assuming your computer
is running FreeBSD at the time) to quickly find files based on name only.
```

#### FreeBSD Tip: #56

```
To erase a line you've written at the command prompt, use "Ctrl-U".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #57

```
To find out the hostname associated with an IP address, use

	dig -x IP_address
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #58

```
To obtain a neat PostScript rendering of a manual page, use ``-t'' switch
of the man(1) utility: ``man -t <topic>''.  For example:

	man -t grep > grep.ps	# Save the PostScript version to a file
or
	man -t printf | lp	# Send the PostScript directly to printer
```

#### FreeBSD Tip: #59

```
To quickly create an empty file, use "touch filename".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #60

```
To read a compressed file without having to first uncompress it, use
"zcat" or "zless" to view it.
		-- Dru <genesis at istar.ca>
```

<!--nextpage-->

#### FreeBSD Tip: #61

```
To repeat the last command in the C shell, type "!!".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #62

```
To save disk space in your home directory, compress files you rarely
use with "gzip filename".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #63

```
To search for files that match a particular name, use find(1); for example

	find / -name "*GENERIC*" -ls

will search '/', and all subdirectories, for files with 'GENERIC' in the name.
      	--  Stephen Hilton <nospam at hiltonbsd.com>
```

#### FreeBSD Tip: #64

```
To see all of the directories on your FreeBSD system, type

	find / -type d | less

All the files?

	find / -type f | less
```

#### FreeBSD Tip: #65

```
To see how long it takes a command to run, type the word "time" before the
command name.
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #66

```
To see how much disk space is left on your partitions, use

	df -h
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #67

```
To see the 10 largest files on a directory or partition, use

	du /partition_or_directory_name | sort -rn | head
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #68

```
To see the IP addresses currently set on your active interfaces, type
"ifconfig -u".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #69

```
To see the last 10 lines of a long file, use "tail filename". To see the
first 10 lines, use "head filename".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #70

```
To see the last time that you logged in, use lastlogin(8).
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #71

```
To see the MAC addresses of the NICs on your system, type

	ifconfig -a
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #72

```
To see the output from when your computer started, run dmesg(8).  If it has
been replaced with other messages, look at /var/run/dmesg.boot.
		-- Francisco Reyes <lists at natserv.com>
```

#### FreeBSD Tip: #73

```
Want colour in your directory listings?  Use "ls -G".  "ls -F" is also useful,
and they can be combined as "ls -FG".
```

#### FreeBSD Tip: #74

```
Want to find a specific port, just type the following under /usr/ports
or one its subdirectories:

	make search name=<port-name>
    or
	make search key=<keyword>
```

#### FreeBSD Tip: #75

```
Want to know how many words, lines, or bytes are contained in a file? Type
"wc filename".
		-- Dru <genesis at istar.ca>
```

#### FreeBSD Tip: #76

```
Want to see how much virtual memory you're using? Just type "swapinfo" to
be shown information about the usage of your swap partitions.
```

#### FreeBSD Tip: #77

```
Want to strip UTF-8 BOM(Byte Order Mark) from given files?

	sed -e '1s/^xefxbbxbf//' < bomfile > newfile
```

#### FreeBSD Tip: #78

```
Want to use sed(1) to edit a file in place?  Well, to replace every 'e' with
an 'o', in a file named 'foo', you can do:

	sed -i.bak s/e/o/g foo

And you'll get a backup of the original in a file named 'foo.bak', but if you
want no backup:

	sed -i '' s/e/o/g foo
```

#### FreeBSD Tip: #79

```
When you've made modifications to a file in vi(1) and then find that
you can't write it, type ``<ESC>!rm -f %'' then ``:w!'' to force the
write

This won't work if you don't have write permissions to the directory
and probably won't be suitable if you're editing through a symbolic link.
```

#### FreeBSD Tip: #80

```
You can adjust the volume of various parts of the sound system in your
computer by typing 'mixer <type> <volume>'.  To get a list of what you can
adjust, just type 'mixer'.
```

#### FreeBSD Tip: #81

```
You can automatically download and install binary packages by doing

	pkg_add -r <URL>

where you replace <URL> with the URL to the package.  This will also
automatically install the packages the package you download is dependent on
(ie, the packages it needs in order to work.)
```

#### FreeBSD Tip: #82

```
You can change the video mode on all consoles by adding something like
the following to /etc/rc.conf:

	allscreens="80x30"

You can use "vidcontrol -i mode | grep T" for a list of supported text
modes.
		-- Konstantinos Konstantinidis <kkonstan at duth.gr>
```

#### FreeBSD Tip: #83

```
You can disable tcsh's terminal beep if you `set nobeep'.
```

#### FreeBSD Tip: #84

```
You can get a good generic server install by using the
instant-server port/package.  If you have ports installed, you can
install it by doing

	# cd /usr/ports/misc/instant-server
	# make install && make clean

as root.  This will install a collection of packages that is appropriate for
running a "generic" server.
```

#### FreeBSD Tip: #85

```
You can install extra packages for FreeBSD by using the ports system.
If you have installed it, you can download, compile, and install software by
just typing

	# cd /usr/ports/<category>/<portname>
	# make install && make clean

as root.  The ports infrastructure will download the software, change it so
it works on FreeBSD, compile it, install it, register the installation so it
will be possible to automatically uninstall it, and clean out the temporary
working space it used.  You can remove an installed port you decide you do not
want after all by typing

	# cd /usr/ports/<category>/<portname>
	# make deinstall

as root.
```

#### FreeBSD Tip: #86

```
You can look through a file in a nice text-based interface by typing

	less filename
```

#### FreeBSD Tip: #87

```
You can make a log of your terminal session with script(1).
```

#### FreeBSD Tip: #88

```
You can often get answers to your questions about FreeBSD by searching in the
FreeBSD mailing list archives at

	http://www.FreeBSD.org/search/search.html
```

#### FreeBSD Tip: #89

```
You can open up a new split-screen window in (n)vi with :N or :E and then
use ^w to switch between the two.
```

#### FreeBSD Tip: #90

```
You can permanently set environment variables for your shell by putting them
in a startup file for the shell.  The name of the startup file varies
depending on the shell - csh and tcsh uses .login, bash, sh, ksh and zsh use
.profile.  When using bash, sh, ksh or zsh, don't forget to export the
variable.
```

<!--nextpage-->

#### FreeBSD Tip: #91

```
You can press Ctrl-D to quickly exit from a shell, or logout from a
login shell.
		-- Konstantinos Konstantinidis <kkonstan at duth.gr>
```

#### FreeBSD Tip: #92

```
You can press Ctrl-L while in the shell to clear the screen.
```

#### FreeBSD Tip: #93

```
You can press up-arrow or down-arrow to walk through a list of
previous commands in tcsh.
```

#### FreeBSD Tip: #94

```
You can search for documentation on a keyword by typing

	apropos keyword
```

#### FreeBSD Tip: #95

```
You can `set autologout = 30' to have tcsh log you off automatically
if you leave the shell idle for more than 30 minutes.
```

#### FreeBSD Tip: #96

```
You can use aliases to decrease the amount of typing you need to do to get
commands you commonly use.  Examples of fairly popular aliases include (in
Bourne shell style, as in /bin/sh, bash, ksh, and zsh):

	alias lf="ls -FA"
	alias ll="ls -lA"
	alias su="su -m"

In csh or tcsh, these would be

	alias lf ls -FA
	alias ll ls -lA
	alias su su -m

To remove an alias, you can usually use 'unalias aliasname'.  To list all
aliases, you can usually type just 'alias'.
```

#### FreeBSD Tip: #97

```
You can use /etc/make.conf to control the options used to compile software
on this system.  Example entries are in
/usr/share/examples/etc/make.conf.
```

#### FreeBSD Tip: #98

```
You can use "pkg_info" to see a list of packages you have installed.
		-- Konstantinos Konstantinidis <kkonstan at duth.gr>
```

#### FreeBSD Tip: #99

```
You can use the 'fetch' command to retrieve files over ftp or http.

	 fetch http://www.FreeBSD.org/index.html

will download the front page of the FreeBSD web site.
```

#### FreeBSD Tip: #100

```
You can use "whereis" to search standard binary, manual page and source
directories for the specified programs. This can be particularly handy
when you are trying to find where in the ports tree an application is.

Try "whereis firefox" and "whereis whereis".
		-- Konstantinos Konstantinidis <kkonstan at duth.gr>
```

#### FreeBSD Tip: #101

```
Want to run the same command again?
In tcsh you can type "!!"
```

#### FreeBSD Tip: #102

```
Want to go the directory you were just in?
Type "cd -"
```