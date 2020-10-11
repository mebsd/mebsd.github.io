---
id: 327
title: 'Perl lock file &#8211; without touching a file'
date: 2011-11-16T13:37:12+00:00
author: Jake
layout: post
guid: http://www.mebsd.com/?p=327
permalink: /coding-snipits/perl-lock-file-without-touching-a-file.html
parent: Perl
grand_parent: code
categories:
  - Coding
  - Perl Programming
tags:
  - code
  - perl examples
  - programming
  - shell script
---
If you have any scripts in your crontab that are run regularly but sometimes run for too long and end up running twice you need something like a lock file/pid file. This can get messy if scripts crash, servers reboot, etc and the lock file doesn&#8217;t get remove and &#8211; oh no &#8211; the script hasn&#8217;t been running all day&#8230;

Another solution to locking a file from being executed more than once in perl is like this. 

<pre class="prettyprint notranslate">#!&#47;usr&#47;bin&#47;perl

use Fcntl &#39;:flock&#39;; # import LOCK_* constants

INIT{
  open  *{0}
        or die &#34;What!? $0:$!&#34;;
  flock *{0}, LOCK_EX|LOCK_NB
    or die &#34;$0 is already running somewhere!n&#34;;
}
</pre>

Give it a try :)