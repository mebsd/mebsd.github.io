---
id: 1208
title: 'PHP IP Calculator'
date: 2013-09-13T11:19:19+01:00
author: Jake
layout: default
guid: https://mebsd.com/?p=1208
permalink: /coding-snipits/php-ipcalc-coding-subnets-ip-addresses.html
parent: Network
grand_parent: PHP
image: /wp-content/uploads/2013/09/3an.gif
categories:
  - Coding
  - PHP Programming
tags:
  - code
  - network
  - php examples
  - programming
  - subnet
---
# PHP IP Calculator: Coding with subnets and IP addresses

Perhaps you have an IP and you need to find out if it's part of a subnet, or what the network address is of a subnet. You could use ipcalc (net-mgmt/ipcalc), a handy tool for doing all sorts of calculations on IPs and subnets, but calling exec on a CLI tool and parsing the output is not always ideal.

Working with IP addresses is pretty easy, with the right formular you can get any of information you need. In this post I am going to share some PHP methods for IP address calculation.

#### Convert CIDR to netmask

Slash notation is beautifully simple, but sometimes you need the netmask e.g. 21 = 255.255.248.0

```php
public function cidr2netmask($cidr)
{
        for ($i = 1; $i <= 32; $i++) {
            $bin .= $cidr >= $i ? '1' : '0';
        }

        $netmask = long2ip(bindec($bin));

        if ( $netmask == "0.0.0.0") {
            return false;
        }

    return $netmask;
}
```

#### Get network address from CIDR

If you know the IP and the subnet size, but whats the network address? e.g. 10.0.2.56/21 = 10.0.0.0

```php
public function cidr2network($ip, $cidr)
{
    $network = long2ip((ip2long($ip)) & ((-1 << (32 - (int)$cidr))));
    return $network;
}
```

#### Convert netmask to CIDR

Got a netmask, but require slash notation? e.g. 255.255.255.128 = 25

```php
public function netmask2cidr($netmask)
{
    $bits = 0;
    $netmask = explode(".", $netmask);

    foreach($netmask as $octect) {
        $bits += strlen(str_replace("0", "", decbin($octect)));
    }

    return $bits;
}
```

#### Is IP address in subnet?

e.g. is 10.5.21.30 in 10.5.16.0/20 == true, is 192.168.50.2 in 192.168.30.0/23 == false

```php
public function cidr_match($ip, $network, $cidr)
{
    if ((ip2long($ip) & ~((1 << (32 - $cidr)) - 1) ) == ip2long($network)) {
        return true;
    }

    return false;
}
```

## The CIDR PHP class

The above wrapped in a class.

```php
class cidr
{
    // convert cidr to netmask
    // e.g. 21 = 255.255.248.0
    public function cidr2netmask($cidr)
    {
        for( $i = 1; $i <= 32; $i++ ) {
            $bin .= $cidr >= $i ? '1' : '0';
        }

        $netmask = long2ip(bindec($bin));

        if ( $netmask == "0.0.0.0") {
            return false;
        }

    return $netmask;
    }

    // get network address from cidr subnet
    // e.g. 10.0.2.56/21 = 10.0.0.0
    public function cidr2network($ip, $cidr)
    {
        $network = long2ip((ip2long($ip)) & ((-1 << (32 - (int)$cidr))));

    return $network;
    }

    // convert netmask to cidr
    // e.g. 255.255.255.128 = 25
    public function netmask2cidr($netmask)
    {
        $bits = 0;
        $netmask = explode(".", $netmask);

        foreach($netmask as $octect)
        $bits += strlen(str_replace("0", "", decbin($octect)));

    return $bits;
    }

    // is ip in subnet
    // e.g. is 10.5.21.30 in 10.5.16.0/20 == true
    //      is 192.168.50.2 in 192.168.30.0/23 == false
    public function cidr_match($ip, $network, $cidr)
    {
        if ((ip2long($ip) & ~((1 << (32 - $cidr)) - 1) ) == ip2long($network))
        {
            return true;
        }

    return false;
    }
}
```
