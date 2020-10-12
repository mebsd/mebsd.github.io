---
id: 1035
title: 'Broadcast address from CIDR - PHP/MySQL'
date: 2012-10-12T16:28:50+01:00
author: Jake
layout: default
guid: http://mebsd.com/?p=1035
permalink: /cheat-sheets/broadcast-from-network-cidr-equation-examples.html
parent: Network
grand_parent: PHP
image: /wp-content/uploads/2012/10/cra.png
categories:
  - Cheat Sheets
  - Coding
  - PHP Programming
tags:
  - cidr
  - ip address
  - mysql
  - php examples
  - subnet
---
# Broadcast address from CIDR - PHP/MySQL

When working with subnets in CIDR (Classless Inter-Domain Routing) notation, for example 10.0.0.0/8, you may find yourself needing to know the broadcast address. Normally I would use ipcalc, which can be found in `net-mgmt/ipcalc` within the FreeBSD ports tree. However it is not always conviniant to use a 3rd party program, escpecially when needing to do the conversion inside some code.

So the awnser, a simple equation to get the broadcast address from the network address and cidr.

`BROADCAST = NETWORK + (2^(32-CIDR)) - 1`

_**The broadcast address is the network IP, plus, 32 minus the CIDR, to the power of 2, minus 1.**_

An example of this, find the broadcast address from 192.168.32.64/26

`192.168.32.64 + (2^(32-26)) - 1 = 192.168.32.127`

So, the broadcast address is 192.168.32.127. There is one problem with this example. How is it I can use an IP address as if it were an integer? Before this equation can be executed we must first convert the IP address into an integer. There are many ways to achieve this, in MySQL you would use the `INET_ATON()` function, in PHP you would use `ip2long()`. Lets look at the previous example again, but this time lets use an integer representation of the IP address.

Find the broadcast address from 192.168.32.64/26, where the IP address is first converted to an integer

`3232243776 + (2^(32-26)) - 1 = 3232243839`

_N.B. this conversion was done using a 64 bit CPU. On a 64bit CPU, 192.168.32.64 = 323224377 and on a 32bit CPU, 192.168.32.64 = -1062723457. This make no differance to the equation, it will work on both 32 and 64 bit architectures._

Converting these integers back to an IP address is simple, for MySQL we use `INET_NTOA()` and for PHP we use `long2ip()`.

#### Examples

```php
long2ip(3232243776) = 192.168.32.64  // Network
long2ip(3232243839) = 192.168.32.127 // Broadcast
```

#### Here is an example SQL query to get the broadcast from CIDR

```sql
mysql> SELECT INET_NTOA(INET_ATON('192.168.32.64') + (pow(2, (32-26))-1));
+-------------------------------------------------------------+
| INET_NTOA(INET_ATON('192.168.32.64') + (pow(2, (32-26))-1)) |
+-------------------------------------------------------------+
| 192.168.32.127                                              |
+-------------------------------------------------------------+
1 row in set (0.00 sec)
```

#### Here is an example PHP function to get the broadcast from CIDR

```php
function cidr2broadcast($network, $cidr)
{
    $broadcast = long2ip(ip2long($network) + pow(2, (32 - $cidr)) - 1);

  return $broadcast;
}

// 192.168.0.0/20
$network = "192.168.0.0";
$cidr    = 20;

print(cidr2broadcast($network, $cidr));
```