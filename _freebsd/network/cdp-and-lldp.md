---
title: CDP and LLDP
author: Jake
layout: default
parent: Network
grand_parent: FreeBSD
permalink: /cisco/cdp-and-more-on-freebsd.html
categories:
  - Cisco
  - Configure FreeBSD
  - Network Configuration
tags:
  - cisco devices
  - cisco discovery protocol
  - freebsd server
  - hostname
  - ladvd
  - link advertisement
  - LLDP
  - monitoring system
  - network
---
## CDP and LLDP with ladvd

On a large network with many switches and routers all over the place it can get messy when trying to keep track of all you kit plugged into it. If the kit your looking for is a Cisco you can use CDP (Cisco Discovery Protocol) to advertise the link and general host information to the Cisco it's plugged into. There are many other link advertisement protocols around such as EDP, FDP etc, hopefully we are moving to a standard in the future (LLDP?). But back to CDP, what if that bit of kit isn't a cisco, what if it's a FreeBSD server (or maybe OpenBSD), how can you track it down?

To find devices on a big network I use a CDP poller, to poll all Cisco devices for their CDP neighbors, this information can then be used to do what ever you need, e.g. auto adding DNS for hostname, adding the device to a monitoring system, or even just keeping a record. To integrate FreeBSD into the mix here so my trusty poller will also find my  servers we need to add a simple tool to talk CDP (or any other, LLDP).

The port ladvd will do this job for you, and if you running FreeBSD 8+ it will even update your ifconfig description to let you know what host and port is on the other end (`-z` switch).

ladvd can be found in `/usr/ports/net/ladvd`, but its worth noting that it will probably only compile on FreeBSD 7 and later.

Here is a quick how-to:

#### 1. Update ports tree

```sh
# portsnap fetch update
```

#### 2. Install ladvd from the ports
```sh
# cd /usr/ports/net/ladvd
# make install
```

or

```sh
# portupgrade -N net/ladvd
```

or from packages

```sh
# pkg_add -r ladvd
```

#### 3. Now just configure it in `/etc/rc.conf`

```sh
ladvd_enable="YES"
ladvd_flags="-a"
```

The -a flag will auto detect which link advertisement protocol to use.

If the hostname of your BSD box doesn't resolve to anything you will need to make sure it does before you can start ladvd, simply adding it into your /etc/hosts will do the trick.

#### 4. Start ladvd

```sh
# /usr/local/etc/rc.d/ladvd start
```

Now you can head over to your managed switch and show it's CDP neighbors to see the FreeBSD server is talking CDP!