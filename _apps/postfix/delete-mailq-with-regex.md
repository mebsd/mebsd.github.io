---
id: 394
title: 'Delete mailq with regex'
date: 2011-11-21T17:15:27+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=394
permalink: /coding-snipits/postfix-delete-mail-from-mailq-with-regex-example-perl-script.html
parent: Postfix
grand_parent: apps
categories:
  - Coding
  - Perl Programming
  - Postfix Configuration
tags:
  - code
  - mailq
  - perl examples
  - postfix
  - postsuper
  - programming
---
# Postfix delete mail from mailq with regex

We found this little perl script somewhere on the internet a while back, I don't remember the original author so unfortunately cannot give credit.

This has saved me a quite few times when a server has been mail bombed and the mail queue is full up and getting stuck. Sure you can use mailq and postsuper the clear down the mail queue manually or with cli magic, but this way is so much neater. as you can see from the example if its just one domain causing issues but you want to keep mails for other mails it's as simple as

```sh
# ./deletemail.pl *@domain.com
```

```perl
#!/usr/bin/perl

$REGEXP = shift || die "no email-adress given (regexp-style, e.g. bl.*@yahoo.com)!";

@data = qx;
for (@data) {
  if (/^(w+)(*|!)?s/) {
     $queue_id = $1;
  }
  if($queue_id) {
    if (/$REGEXP/i) {
      $Q{$queue_id} = 1;
      $queue_id = "";
    }
  }
}

open(POSTSUPER,"|postsuper -d -") || die "couldn't open postsuper" ;

foreach (keys %Q) {
  print POSTSUPER "$_n";
};
close(POSTSUPER);
```

For speed it better if postfix is running when you use the script, but that should be taken as read when using postsuper as is in this script.