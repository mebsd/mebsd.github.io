---
id: 423
title: 'Inches to centimeters'
date: 2011-11-26T20:25:37+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=423
permalink: /coding-snipits/c-programming-inches-to-cm-inches-to-centimeters-example.html
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
# Inches to centimeters

This is an example of how to convert Inches to cm (Inches to centimeters) using the C programming language, 1 inch = 2.54 cm.

#### file: inches_cm.c

```c
#include <stdio.h>
#include <stdlib.h>

int main()
{

    float in_cm;
    char  in_inches[4];

    printf("Convert inches: ");
    scanf("%s", &in_inches);

    in_cm = atoi(in_inches)*2.54;

    printf("%.2f cmn", in_cm);
    return(0);

}
```

To compile the code with gcc:

```sh
$ gcc inches_cm.c -o inches_cm
```

Exmaple run:

```sh
$ ./inches_cm
Convert inches: 12
30.48 cm
$ ./inches_cm
Convert inches: 563478
1431234.12 cm
```