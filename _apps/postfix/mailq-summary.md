---
id: 398
title: 'Postfix mailq summary'
date: 2011-11-21T17:26:24+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=398
permalink: /coding-snipits/postfix-mailq-summary-fast-perl-script.html
parent: Postfix
grand_parent: apps
categories:
  - Coding
  - Configure FreeBSD
  - Perl Programming
  - Postfix Configuration
tags:
  - code
  - perl examples
  - postfix
  - programming
---
# Postfix mailq summary

Here is a nice perl script to summarise what's in your mailq, it's pretty quick too - much quicker than waiting for the footer from mailq command. This is an example output:

```sh
# ./mailq.pl
Incoming: 2
Active: 1
Deferred: 13
```

File: mailq.pl
```perl
#!/usr/bin/perl -w

use strict;
use warnings;
use Symbol;

    # count mailq function
    sub count
    {
            my ($dir) = @_;
            my $dh = gensym();
            my $c = 0;
            opendir($dh, $dir) or die "$0: opendir: $dir: $!n";
            while (my $f = readdir($dh))
            {
                if ($f =~ m{^[A-F0-9]{5,}$})
                {
                    ++$c;
                }
                elsif ($f =~ m{^[A-F0-9]$})
                {
                    $c += count("$dir/$f");
                }
            }
            closedir($dh) or die "closedir: $dir: $!n";
            return $c;
        }

    # call mailq count function for queues
    my $qdir = "/var/spool/postfix";
    chdir($qdir) or die "$0: chdir: $qdir: $!n";
    my $incomming = count("incoming");
    my $active    = count("active");
    my $deferred  = count("deferred");

# print mailq
printf "Incoming: %dn", $incomming;
printf "Active: %dn", $active;
printf "Deferred: %dn", $deferred;
```