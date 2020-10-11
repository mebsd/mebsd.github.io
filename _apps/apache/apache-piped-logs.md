---
title: Apache piped logs
date: 2010-10-30T04:40:35+01:00
author: Jake
layout: default
parent: Apache
grand_parent: apps
permalink: /coding-snipits/apache-piped-logs.html
categories:
  - Apache Configuration
  - Coding
  - Perl Programming
tags:
  - apache
  - apache piped logs
  - log archiving
  - mass virtual hosting
  - mod_vhost_alias
  - split logs
---
## Apache piped logs
Create a log file for each site on a mass virtual hosting setup, using mod\_vhost\_alias and store in a structured directory, e.g. `/var/log/apache/www.domain.com/2010/10/30/access_log`, creating the directories as needed. 

Apache allows you to customise alot of things out of the box with you http logs, by if you are using mod\_vhost\_alias you might find that your options become limited, one big log or nothing. Mass virtual hosting with mod_vhost_alias is generally used when you want to host many hosts with a single virtual host. 

There are some limitations, such as using the same syntax in CustomLog as you do in VirtualDocumentRoot, to get around this one approach could be to use piped logs...

File: `/usr/local/bin/vhost_access.pl`

```perl
#!/usr/bin/perl

use strict;

my $logEntry;
my $accessLogName = "access_log";
my $logDir = "/var/log/apache";
my $vhostLogDir;

sub writeLog;

while( <> )
{

    $logEntry = $_;

    # get the vhost from this log entry:
    $logEntry=~/(.*?) /;
    my $vhost = $1;
    my ($year, $month, $day) = ((localtime)[5]+1900,
                               sprintf("%02d", (localtime)[4]+1),
                               sprintf("%02d", (localtime)[3])
    );

    # remove 'www'. from domain path
    $vhost =~ s!^www.?!!i;

    # Name of access logfiles:
    $vhostLogDir = "$logDir/$vhost/$year/$month/$day";
    writeLog($vhostLogDir);
}

# write a log entry to a file
sub writeLog()
{
    my $logDir = shift @_;

    if( ! -d $logDir )
    {
        `/bin/mkdir -p $logDir`;
    }

    open(FD, ">>$logDir/$accessLogName");

    # strip off the vhost data:
    $logEntry =~s/.*? //;

    print FD $logEntry;
    close FD;
}
```

Next add the following line into your httpd.conf (It does **not** need to be inside the `<VirtualHost>` tag).

```sh
LogFormat "%V %h %l %u %t "%r" %>s %b "%{Referer}i" "%{User-Agent}i"" vcombined
CustomLog "|/usr/local/bin/vhost_access.pl" vcombined
```

Now restart Apache. By doing `ps ax` or equivalent should now show the additional process for `vhost_access.pl`