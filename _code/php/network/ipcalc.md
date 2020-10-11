---
id: 1208
title: 'PHP IP Calculator: Coding with subnets and IP addresses'
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
Working with IP address and subnets in code is a day to day task for a network administrator. Say you have an IP and you need to find out if it&#8217;s part of a subnet, or what the network address is of a subnet, what&#8217;s the best way? In the past I would reach for ipcalc (net-mgmt/ipcalc), a handy little tool for doing all sorts of calculations on IPs and subnets, but calling exec on a CLI tool and parsing the output is not exactly efficient.

The truth is, working with IP addresses is pretty easy, with the right formular you can get any of information you need. In this post I am going to share a little PHP class with some of my favourite IP address calculation methods. Cue the PHP IP calculator.

## 1. Convert CIDR to netmask

Slash notation is beautifully simple, but some things just insist on netmasks. e.g. 21 = 255.255.248.0

<pre class="notranslate prettyprint">public function cidr2netmask($cidr)
    {
        for( $i = 1; $i &lt;= 32; $i++ )
        $bin .= $cidr &gt;= $i ? '1' : '0';

        $netmask = long2ip(bindec($bin));

        if ( $netmask == "0.0.0.0")
        return false;

    return $netmask;
    }
</pre>

## 2. Get network address from cidr subnet

If you know the IP and the subnet size, but whats the network address? e.g. 10.0.2.56/21 = 10.0.0.0

<pre class="notranslate prettyprint">public function cidr2network($ip, $cidr)
    {
        $network = long2ip((ip2long($ip)) & ((-1 &lt;&lt; (32 - (int)$cidr))));

    return $network;
    }
</pre>

## 3. Convert netmask to CIDR

Got a netmask, but require slash notation? e.g. 255.255.255.128 = 25

<pre class="notranslate prettyprint">public function netmask2cidr($netmask)
    {
        $bits = 0;
        $netmask = explode(".", $netmask);

        foreach($netmask as $octect)
        $bits += strlen(str_replace("0", "", decbin($octect)));

    return $bits;
    }
</pre>

## 4. Is IP address in subnet?

e.g. is 10.5.21.30 in 10.5.16.0/20 == true, is 192.168.50.2 in 192.168.30.0/23 == false

<pre class="notranslate prettyprint">public function cidr_match($ip, $network, $cidr)
    {
        if ((ip2long($ip) & ~((1 &lt;&lt; (32 - $cidr)) - 1) ) == ip2long($network))
        {
            return true;
        }

    return false;
    }
</pre>

## The CIDR PHP class

And for completeness, here is the full class.

<pre class="notranslate prettyprint">class cidr
{
    // convert cidr to netmask
    // e.g. 21 = 255.255.248.0
    public function cidr2netmask($cidr)
    {
        for( $i = 1; $i &lt;= 32; $i++ )
        $bin .= $cidr &gt;= $i ? '1' : '0';

        $netmask = long2ip(bindec($bin));

        if ( $netmask == "0.0.0.0")
        return false;

    return $netmask;
    }

    // get network address from cidr subnet
    // e.g. 10.0.2.56/21 = 10.0.0.0
    public function cidr2network($ip, $cidr)
    {
        $network = long2ip((ip2long($ip)) & ((-1 &lt;&lt; (32 - (int)$cidr))));

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
        if ((ip2long($ip) & ~((1 &lt;&lt; (32 - $cidr)) - 1) ) == ip2long($network))
        {
            return true;
        }

    return false;
    }
}
</pre>