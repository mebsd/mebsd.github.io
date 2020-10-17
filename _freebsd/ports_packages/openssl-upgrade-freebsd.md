---
title: Keep OpenSSL updated FreeBSD
author: Jake
layout: default
parent: Ports & Packages
grant_parent: freebsd
permalink: /freebsd-security-hardening/openssl-upgrade-freebsd.html
categories:
  - BSD Hardening
  - Build Your FreeBSD World
  - System Configuration
tags:
  - freebsd server
  - freebsd-update
  - upgrade openssl package
---
# OpenSSL Upgrade

With the base install of FreeBSD you get a copy on OpenSSL installed in `/usr/bin/openssl`, however it is not a registered package. This makes upgrading OpenSSL a little different, you must first install the OpenSSL port and then tell your make.conf to use the port install when using OpenSSL libraries. Once this is done you can keep OpenSSL up to date just as you would with any other port.

First make sure your port tree is up to date

```sh
# portsnap fetch update
```

Then add the following line to /etc/make.conf

```sh
WITH_OPENSSL_PORT=yes
```

Now install the OpenSSL port from `/usr/ports/security/openssl` or if you like to use portupgrade

```sh
# portupgrade -N security/openssl
```

And that's about it, due to the additional line in make.conf any port built with OpenSSL will use the port (latest) version. You may need to recompile other packages using OpenSSL, in that case this command could help out

```sh
# portupgrade -Rrf security/openssl
```