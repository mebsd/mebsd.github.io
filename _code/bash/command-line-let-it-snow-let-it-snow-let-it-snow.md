---
id: 483
title: 'Command line snow'
date: 2011-12-21T17:16:02+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=483
permalink: /coding-snipits/command-line-let-it-snow-let-it-snow-let-it-snow.html
parent: Bash
grand_parent: code
categories:
  - Coding
  - Commands
  - Shell Scripting
tags:
  - cli
  - code
---
# Command line snow
## Let it snow, Let it snow, Let it snow


Do you want to make your bash terminal snow? Here is a fun little way to do that using awk.

This should be safe to copy and paste right into your bash cli and hit enter (takes a few seconds for it to start snowing)

```sh
clear; \
while :; \
do \
echo $LINES $COLUMNS $(($RANDOM%$COLUMNS)); \
sleep 0.03; \
done | \
awk '{ \
         a[$3]=0; \
         for(x in a) {o=a[x]; \
                      a[x]=a[x]+1; \
                      printf "\033[%s;%sH ",o,x; \
                      printf "\033[%s;%sH*\033[0;0H",a[x],x; \
                     } \
     }'  
```

Which should give you something like this, but animated!

```
*
                                 *
                      *
                                                                           *
                                                                                                             *
                                                           *
                                           *
                                                                                                                            *
                                                                                          *
      *
                      *                                                                                           *
                                                                            *
                                                                                                                     *
                                                                             *
                                                                                              *
                                                                                *
               *
                                                               *
*                                                                   *
**** *   * ******     **   * ** *  *   ***  ** **     ** **  ** ** *   * *****  * *** ******* *    *** *  * ** * *  *** *     **
```

Merry Xmas!