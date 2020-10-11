---
title: Update FreeBSD source tree using subversion svn
author: Jake
layout: default
parent: Source
grand_parent: freebsd
permalink: /make-build-your-freebsd-word/update-freebsd-source-tree-using-subversion-svn.html
categories:
  - Build Your FreeBSD World
  - Cheat Sheets
  - Configure FreeBSD
  - System Configuration
tags:
  - freebsd 9.0
  - freebsd server
  - freebsd-update
---
## Update FreeBSD source tree using subversion svn
In a previous post I described the process for updating the FreeBSD source tree using csup and upgrading from FreeBSD release to the stable branch. These days subversion svn has started to replace cvs as the version control system for the FreeBSD source tree. Here I am going to note the commands you would use to updating the FreeBSD source tree using subversion instead of csup.

First you will need subversion installed on the system, to do this you can install from the ports or packages.

From ports:

```sh
# cd /usr/ports/devel/subversion
# make install
```


From package:

```sh
# pkg_add -r subversion
```


You can just accept the default port configuration options. Once installed you can then update the FreeBSD source tree using this command, this will update the source tree to FreeBSD 9-STABLE, the same as the RELENG_9 tag used in csup.

```sh
# svn co svn://svn.freebsd.org/base/stable/9 /usr/src
```


Other examples, to update to the latest HEAD.

```sh
# svn co svn://svn.freebsd.org/base/head /usr/src
```


Or even get the source for an old release.

```sh
# svn co svn://svn.freebsd.org/base/release/7.4.0 /usr/src
```


To find paths to other repositories you can checkout see the FreeBSD viewvc pages, here <a href="http://svnweb.freebsd.org/base/" title="[base] Index of /" target="_blank">http://svnweb.freebsd.org/base/</a>.

Once the source tree is checked out with subversion the path and options used from the check out is stored in the new /usr/src/.svn directory. Thanks to this all it takes to update the FreeBSD source tree in the future is this command.

```sh
# svn up /usr/src
```


This will update the source to the latest revision and, of course, only copy the files that are new or have changed. If you need to find out the revision of the source you currently have you can use this command, (there are other ways to get the revision number also).

```sh
# svn info /usr/src
Path: .
Working Copy Root Path: /usr/src
URL: svn://svn.freebsd.org/base/stable/9
Repository Root: svn://svn.freebsd.org/base
Repository UUID: ccf9f872-aa2e-dd11-9fc8-001c23d0bc1f
Revision: 228954
Node Kind: directory
Schedule: normal
Last Changed Author: cperciva
Last Changed Rev: 228843
Last Changed Date: 2011-12-23 15:00:37 +0000 (Fri, 23 Dec 2011)
```