---
id: 813
title: 'Square number generator'
date: 2012-04-10T19:16:02+01:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=813
permalink: /coding-snipits/c-programming-squre-number-generator.html
parent: C
grand_parent: code
categories:
  - C Programming
  - Coding
tags:
  - c programming
  - programming
---
# Square number generator

This is an example of how to square numbers using a simple function in the C programming language.

#### file: square.c

```c
#include <stdio.h>

int square(int x)
{
    int sq;

    sq = x * x;
    return sq;
}

int main()
{
    int i, count = 10;

    for(i = 1; i <= count; ++i)
    {
        printf("%dn", square(i));
    }

return(0);
}
```

#### Compile and run

```sh
$ gcc -o square square.c
$ ./square
1
4
9
16
25
36
49
64
81
100
```

Squaring a number is, of course, as simple as multiplying a number by itself. If you ever need to do this using the C programming language, the above is a simple proof of concept.