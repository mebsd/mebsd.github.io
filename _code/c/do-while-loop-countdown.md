---
id: 420
title: 'do while loop countdown'
date: 2011-11-26T20:13:35+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=420
permalink: /coding-snipits/c-programming-do-while-loop-countdown-example.html
parent: C
grand_parent: code
categories:
  - C Programming
  - Coding
tags:
  - c programming
  - code
  - programming
---
# do while loop countdown

#### Quote from The C Programming Language, Second Edition by Brian W. Kernighan & Dennis M. Ritchie

> *&#8220;..the while and for loops test the termination condition at the top. By contrast, the third loop in C, the do-while, tests at the bottom after making each pass through the loop body; the body is always executed at least once.&#8221;*

A simple countdown timer example using a do while loop in C

#### file: countdown.c

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    int start;

    do
    {
        printf("Need Number to start the countdown (1 - 100): ");
        scanf("%d",&start);
    }
    while(!(start<100));

    do
    {
        printf("T-minus %d\n",start);
        start--;
        sleep(1);
    }
    while(start>0);

    printf("Zero!\nBlast Off!\n");
    return(0);
}
```

To compile using gcc compiler:

```sh
$ gcc countdown.c -o countdown
```

Example output:

```sh
$ ./countdown
Need Number to start the countdown (1 - 100): 5
T-minus 5
T-minus 4
T-minus 3
T-minus 2
T-minus 1
Zero!
Blast Off!
```

This is a basic example, and only one of the many ways this task could be completed in the C language.