---
id: 408
title: Protecting FreeBSD with sysctl
date: 2011-11-22T10:44:04+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=408
permalink: /freebsd-security-hardening/protecting-freebsd-with-sysctl-101.html
parent: Security
grand_parent: freebsd
categories:
  - BSD Hardening
  - Configure FreeBSD
  - System Configuration
tags:
  - freebsd server
  - security
---
# Protecting FreeBSD with sysctl

There are a few additional sysctl's that I like to put in for shared hosted servers for some additional security, in general it's not a good thing if customer A can see anything related to customer B on the server they share. On a mass hosting environment where jails are not practical due to the sheer number of domains we need to use other methods of restricting access between users.

One of the things we can do to tighten things up is to stop a user from seeing any running processes belonging to any other user. All server side scripts are ran as the owner of the script, these are virtual users and I will discus how this is done in a later post. The sysctl to do this should actually be in your default `/etc/sysctl.conf` but hashed out. To use it just uncomment.

```sh
# Uncomment this to prevent users from seeing information about processes that
# are being run under another UID.
security.bsd.see_other_uids=0
```

Once enabled it might be a good idea to explain to the other sysadmins that if they are not root they will only see processes owned by themselves, and not to worry :)

The next thing we are going to look at are black holes. Basically what do we want to do with packets that are received on a closed port. There are two blackhole sysctls we can set, one for TCP and one for UDP, first we'll look at the one for TCP.

These are the possible values

  * **1** = SYN packets arriving on a closed port will be dropped without a RST packet being sent back.
  * **2** =  All packets arriving on a closed port are dropped without an RST being sent back.

As the packets are dropped and no packets are sent back in reply you will have a bit or CPU time. Here what it looks like in the configuration and when applied.

```sh
# cat /etc/sysctl.conf | grep tcp.blackhole
net.inet.tcp.blackhole=2
# sysctl net.inet.tcp.blackhole
net.inet.tcp.blackhole: 2
# sysctl -d net.inet.tcp.blackhole
net.inet.tcp.blackhole: Do not send RST on segments to closed ports
```

Now moving on to the UDP blackhole. UDP is fire and forget, it does not have states and so there is only one option for it's sysctl.

  * **1** = All UDP packets arriving on a closed port will be dropped.

Here is some output from the server to give you some more information.

```sh
# cat /etc/sysctl.conf  | grep udp.blackhole
net.inet.udp.blackhole=1
# sysctl net.inet.udp.blackhole
net.inet.udp.blackhole: 1
# sysctl -d net.inet.udp.blackhole
net.inet.udp.blackhole: Do not send port unreachables for refused connects
```

The final sysctl I can going to mention in this post is `net.inet.ip.random_id`, this one is to do with the way packets are marked when they leave the server, here's a quote from kris, dwmalone, OpenBSD.

> The IP_ID is used for packet reassembly and needs to be unique within a certain time  
> frame specific to a certain host. Normally the IP_ID is assigned sequentially to each IP  
> packet leaving the host. This makes it possible to gather for example the number of  
> hosts behind a NAT device (track different sequences of IP_ID’s). Enabling random  
> IP\_ID’s assigns a random IP\_ID to each packet rendering this kind of “attack”  
> ineffective.

Lets see how it looks configured

```sh
# cat /etc/sysctl.conf | gerp random_id
net.inet.ip.random_id=1
# sysctl net.inet.ip.random_id
net.inet.ip.random_id: 1
 # sysctl -d net.inet.ip.random_id
net.inet.ip.random_id: Assign random ip_id values

```