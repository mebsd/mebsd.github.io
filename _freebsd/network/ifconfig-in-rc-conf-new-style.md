---
id: 306
title: 'FreeBSD: New improved rc.conf syntax for IPv4 ifconfig alias'
date: 2015-03-02T15:39:19+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=306
permalink: /configure-freebsd-servers/freebsd-ifconfig-in-rc-conf-new-style.html
parent: Network
grand_parent: freebsd
categories:
  - Configure FreeBSD
  - Network Configuration
  - System Configuration
tags:
  - freebsd 9.0
  - freebsd server
  - ifconfig
  - network
---
# New rc.conf syntax for IPv4 ifconfig alias

_This page used to document to use of, ipv4\_addrs\_<interface>, which is now depreciated. The functionality has been integrated into ifconfig\_<IF>\_alias. <a href="http://svnweb.freebsd.org/base/head/share/man/man5/rc.conf.5?r1=251584&#038;r2=252015" target="_blank">See SVN diff</a>_

Quote man page: **&#8220;Note that ipv4\_addrs\_<interface> variable was supported for IPv4 CIDR address notation. It is now deprecated because the functionality was integrated into ifconfig\_<interface>\_alias<n> though ipv4\_addrs\_<interface> is still supported for backward compatibility.&#8221;**

#### This is an example of the new rc.conf syntax for configuring IPv4 addresses

```sh
ifconfig_fxp0_aliases="inet 192.168.0.1/24 inet 192.168.0.2-5/32"
```

The above example will assign the IP address 192.168.0.1 with a netmask of 255.255.255.0 (/24). It will also add the aliases 192.168.0.2,192.168.0.3,..,192.168.0.5 with a netmask of 255.255.255.255 (/32).

#### After rc is executed the output of **ifconfig fpx0** will look like this:

```sh
# ifconfig fxp0
fxp0: flags=8843<up,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=4219b<rxcsum,TXCSUM,VLAN_MTU,VLAN_HWTAGGING,VLAN_HWCSUM,TSO4,WOL_MAGIC,VLAN_HWTSO>
	ether 00:0e:0c:62:aa:b7
	inet 192.168.0.1 netmask 0xffffff00 broadcast 192.168.0.255
	inet 192.168.0.2 netmask 0xffffffff broadcast 192.168.0.2
	inet 192.168.0.3 netmask 0xffffffff broadcast 192.168.0.3
	inet 192.168.0.4 netmask 0xffffffff broadcast 192.168.0.4
	inet 192.168.0.5 netmask 0xffffffff broadcast 192.168.0.5
	media: Ethernet autoselect (100baseTX <full-duplex>)
	status: active
```

#### Full extract from [rc.conf(5)](http://www.mebsd.com/man/rc.conf/5 "rc.conf(5) man page") man page:

```
It also possible to configure multiple IP addresses in Class-
                 less Inter-Domain Routing (CIDR) address notation, whose each
                 address component can be a range like inet 192.0.2.5-23/24 or
                 inet6 2001:db8:1-f::1/64.  This notation allows address and
                 prefix length part only, not the other address modifiers.
                 Note that the maximum number of the generated addresses from
                 a range specification is limited to an integer value speci-
                 fied in netif_ipexpand_max in rc.conf(5) because a small typo
                 can unexpectedly generate a large number of addresses.  The
                 default value is 2048.  It can be increased by adding the
                 following line into rc.conf(5):

                 netif_ipexpand_max="4096"

                 In the case of 192.0.2.5-23/24, the address 192.0.2.5 will be
                 configured with the netmask /24 and the addresses 192.0.2.6
                 to 192.0.2.23 with the non-conflicting netmask /32 as
                 explained in the ifconfig(8) alias section.  Note that this
                 special netmask handling is only for inet, not for the other
                 address families such as inet6.

                 With the interface in question being ed0, an example could
                 look like:

                 ifconfig_ed0_alias2="inet 192.0.2.129/27"
                 ifconfig_ed0_alias3="inet 192.0.2.1-5/28"

                 and so on.

                 Note that ipv4_addrs_<interface> variable was supported for
                 IPv4 CIDR address notation.  It is now deprecated because the
                 functionality was integrated into
                 ifconfig_<interface>_alias<n> though ipv4_addrs_<interface>
                 is still supported for backward compatibility.

                 For each ifconfig_<interface>_alias<n> entry with an address
                 family keyword, its contents are passed to ifconfig(8).  Exe-
                 cution stops at the first unsuccessful access, so if some-
                 thing like this is present:

                 ifconfig_ed0_alias0="inet 127.0.0.251 netmask 0xffffffff"
                 ifconfig_ed0_alias1="inet 127.0.0.252 netmask 0xffffffff"
                 ifconfig_ed0_alias2="inet 127.0.0.253 netmask 0xffffffff"
                 ifconfig_ed0_alias4="inet 127.0.0.254 netmask 0xffffffff"

                 Then note that alias4 would not be added since the search
                 would stop with the missing ``alias3'' entry.  Because of
                 this difficult to manage behavior, there is
                 ifconfig_<interface>_aliases variable, which has the same
                 functionality as ifconfig_<interface>_alias<n> and can have
                 all of entries in a variable like the following:

                 ifconfig_ed0_aliases="\
                         inet 127.0.0.251 netmask 0xffffffff \
                         inet 127.0.0.252 netmask 0xffffffff \
                         inet 127.0.0.253 netmask 0xffffffff \
                         inet 127.0.0.254 netmask 0xffffffff"

                 It also supports CIDR notation.
```