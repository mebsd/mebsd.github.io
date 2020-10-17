---
id: 630
title: 'pkgng: First look at FreeBSD's new package manager'
date: 2012-02-13T20:50:24+00:00
author: Jake
layout: post
guid: http://www.mebsd.com/?p=630
permalink: /make-build-your-freebsd-word/pkgng-first-look-at-freebsds-new-package-manager.html
parent: Ports & Packages
grand_parent: freebsd
categories:
  - Build Your FreeBSD World
  - Cheat Sheets
  - Ports and Packages
  - System Configuration
tags:
  - freebsd update script
  - packages
  - pkg
  - pkgng
  - ports
---
# pkgng: First look 
## FreeBSD's new package manager

FreeBSD has been long due a better package management system, `pkg\_add`, `pkg\_info`, etc just doesn't cut it any more. For a long time GNU/linux users have always used this as a reason not to use FreeBSD and instead favour some GNU/linux combination with an all encompassing easy to use package manager, such as Debian's apt-get. FreeBSD's response has always been, (not actual quote), "We have the ports collection, which is cooler and more flexible than just having some easy to use package manager. You can always do it yourself by writing scripts around the pkg_* tools or using portmaster's --packages-only option". While this is all true, there is still a gap for a good package manager that needs filling. So here comes pkgng (pkg: (next|new) generation), this is FreeBSD's next generation package manager <del datetime="2012-08-30T21:01:06+00:00">and although still in beta, with a few features missing</del>, it is very nice. 

**pkg (aka pkgng) 1.0 released <a href="http://lists.freebsd.org/pipermail/freebsd-ports/2012-August/077909.html" title="http://lists.freebsd.org/pipermail/freebsd-ports/2012-August/077909.html" target="_blank">http://lists.freebsd.org/pipermail/freebsd-ports/2012-August/077909.html</a>**

Here is a quick overview of pkgng, how to use it and some of the new features that will be available. You might find the official wiki page interesting reading if you haven't seen it already, <a href="http://wiki.freebsd.org/pkgng" title="pkgng - FreeBSD Wiki" target="_blank">pkgng - FreeBSD Wiki</a>.

## Getting started: Install pkgng

First let's install pkgng, (should we be calling it simply 'pkg'?), it is in the FreeBSD ports tree and you can find it under `ports-mgmt/pkg`. If you are running HEAD you will find that pkg is now part of the base system.

#### From ports

```sh
# portsnap fetch update
# cd /usr/ports/ports-mgmt/pkg
# make install clean
```

#### From packages

```sh
# pkg_add -r pkg
Fetching ftp://ftp.freebsd.org/pub/FreeBSD/ports/amd64/packages-9-current/Latest/pkg.tbz... Done.
```

## Getting familiar with pkgng

Now that we have pkgng (pkg) installed we can get used to the new CLI and man pages.

The main CLI utility for pkgng is `pkg`, let's see the usage brief for it:

```sh
# pkg
usage: pkg [-v] [-d] [-j |-c ]  []

Global options supported:
        -d             Increment debug level
        -j             Execute pkg(1) inside a jail(8)
        -c             Execute pkg(1) inside a chroot(8)
        -v             Display pkg(1) version

Commands supported:
        add            Registers a package and installs it on the system
        audit          Reports vulnerable packages
        autoremove     Removes orphan packages
        backup         Backs-up and restores the local package database
        check          Checks for missing dependencies and database consistency
        clean          Cleans old packages from the cache
        create         Creates software package distributions
        delete         Deletes packages from the database and the system
        fetch          Fetches packages from a remote repository
        help           Displays help information
        info           Displays information about installed packages
        install        Installs packages from remote package repositories
        query          Queries information about installed packages
        register       Registers a package into the local database
        remove         Deletes packages from the database and the system
        repo           Creates a package repository catalogue
        rquery         Queries information in repository catalogues
        search         Performs a search of package repository catalogues
        set            Modifies information about packages in the local database
        shell          Opens a debug shell
        shlib          Displays which packages link against a specific shared library
        stats          Displays package database statistics
        update         Updates package repository catalogues
        updating       Displays UPDATING information for a package
        upgrade        Performs upgrades of packaged software distributions
        version        Displays the versions of installed packages
        which          Displays which package installed a specific file

For more information on the different commands see 'pkg help '.
```

First thing we notice is the global options, other than the basic debug and display version flags we can see some jail/chroot options. One of the very cool features of pkgng, you can manage packages in side a jail or chroot from the host OS by providing the Jail ID (JID) or chroot path. The next thing we see are all the subcommands that we can use, including a useful 'help' subcommand that will quickly display the man page (if available for that command). Here's an exmaple of the help command and the available man pages.

```sh
# pkg help
usage: pkg help <command>

Where <command> can be:
        add
        audit
        autoremove
        backup
        check
        clean
        create
        delete
        fetch
        help
        info
        install
        query
        register
        remove
        repo
        rquery
        search
        set
        shell
        shlib
        stats
        update
        updating
        upgrade
        version
        which
```

#### Example usage of the pkgng help command

```
# pkg help add
PKG-ADD(8)              FreeBSD System Manager's Manual             PKG-ADD(8)

NAME
     pkg add -- Registers a package and installs it on the system

SYNOPSIS
     pkg add 
     pkg add :///

DESCRIPTION
     pkg add installs a package from either a local source or a remote one.

     When installing from a remote source you need to specify the protocol to
     use when fetching the package.

     Currently supported protocols are FTP, HTTP and HTTPS.

OPTIONS
     The following options are supported by pkg add:

ENVIRONMENT
     The following environment variables affect the execution of pkg add.  See
     pkg.conf(5) for further description.

     ASSUME_ALWAYS_YES

     HANDLE_RC_SCRIPTS

     PKG_DBDIR

FILES
     See pkg.conf(5).

SEE ALSO
     pkg(8), pkg-audit(8), pkg-autoremove(8), pkg-backup(8), pkg-check(8),
     pkg-clean(8), pkg-create(8), pkg-delete(8), pkg-fetch(8), pkg-info(8),
     pkg-install(8), pkg-query(8), pkg-register(8), pkg-repo(8),
     pkg-rquery(8), pkg-search(8), pkg-set(8), pkg-shell(8), pkg-shlib(8),
     pkg-stats(8), pkg-update(8), pkg-updating(8), pkg-upgrade(8),
     pkg-version(8), pkg-which(8), pkg.conf(5)

FreeBSD 10.0                     June 12, 2012                    FreeBSD 10.0
```

#### The man pages currently installed by pkgng are:

  * man 8 pkg
  * man 8 pkg-add
  * man 8 pkg-autoremove
  * man 8 pkg-backup
  * man 8 pkg-create
  * man 8 pkg-delete
  * man 8 pkg-info
  * man 8 pkg-install
  * man 8 pkg-query
  * man 8 pkg-register
  * man 8 pkg-repo
  * man 8 pkg-search
  * man 8 pkg-set (pkg set added in beta8)
  * man 8 pkg-update
  * man 8 pkg-updating (added in beta6)
  * man 8 pkg-upgrade
  * man 8 pkg-version
  * man 8 pkg-which
  * man 5 pkg.conf

## Pkgng's configuration file: pkg.conf

The last man page listed above is for pkg.conf, this is the system-wide confutation file for pkgng's pkg tools. The configuration file is in YAML format. For more information on the syntax of YAML, please visit the official YAML website - <a href="http://www.yaml.org" title="Official YAML website" target="_blank">http://www.yaml.org</a>.

If you want to try pkgng for yourself now you won't be able to use the official FreeBSD package mirrors, but you can use the beta package server at http://pkgbeta.freebsd.org. This beta server is not kept as upto date as the live repos but it will start you on your way. You could also setup your own package repository which can be used by pkgng, for this you could use poudrier <a href="http://unix-heaven.org/continuous-package-building-with-poudriere-and-jenkins" title="http://unix-heaven.org/continuous-package-building-with-poudriere-and-jenkins" target="_blank">http://unix-heaven.org/continuous-package-building-with-poudriere-and-jenkins</a> or tinderbox <a href="http://www.glenbarber.us/2012/06/11/Maintaining-Your-Own-pkgng-Repository.html" title="http://www.glenbarber.us/2012/06/11/Maintaining-Your-Own-pkgng-Repository.html" target="_blank">http://www.glenbarber.us/2012/06/11/Maintaining-Your-Own-pkgng-Repository.html</a>.

To get pkgng working you **must** first configure a **PACKAGESITE** in `/usr/local/etc/pkg.conf`, or you will get an error like this:

```sh
# pkg update
pkg: Invalid configuration format, ignoring the configuration file
Updating repository catalogue
pkg: PACKAGESITE is not defined.
```

#### file: /usr/local/etc/pkg.conf

```sh
# cat /usr/local/etc/pkg.conf
PACKAGESITE: http://pkgbeta.freebsd.org/freebsd-9-amd64/latest
```

In the near future this value will be set automatically.

If you want to test upgrading packages you might want to start with **PACKAGESITE** defined to **http://pkgbeta.freebsd.org/freebsd-9-amd64/2012-08-12** and then change it to the above when you want to test upgrades.

## pkg2ng - migrating to pkgng

Pkgng has it's own way of keeping track of installed packages. This means to start using pkgng with all your existing packages you need to import them first. To do this pkgng provides a nice shell script which is installed in **/usr/local/sbin/pkg2ng**. Migrating to pkgng is a simple as this.

```sh
# pkg2ng
```

When the script finishes all of your packages will have been registered with pkgng and you can start using it as your new package manager. You will also notice the old `pkg\_*` tools are now obsolete, running `pkg\_info` will show no installed packages. The original `/var/db/pkg` directory is backed up by `pkg2ng` to `/var/db/pkg.bak`.

## Linking ports to pkgng

When you make install a new port, the FreeBSD ports system will register the newly installed port with `pkg\_*`. To make the ports instead register with pkg you need to make an add `WITH\_PKGNG=yes` to `make.conf`.

```sh
# cat /etc/make.conf
WITH_PKGNG=yes
```

## pkg update - update from remote package repository databases

Pkgng uses an sqlite database to store all the information it needs about a repo and the packages it contains. You can create your own for your own database repo by using the `pkg repo <path\_to\_repo>` command. This is one of the reasons why you cannot use pkgng with the official mirrors yet, this package `repo.txz` and therefore the file `repo.sqlite` does not exist yet.

Assuming you have **PACKAGESITE** setup as described in the `pkg.conf` section or set to a similar package site, maybe your own? You can now update pkgng from the remote repository database.

#### You should always do this before doing a remote install or upgrades.

```sh
# pkg update
Updating repository catalogue
repo.txz                         100% 9055KB   8.8MB/s   6.1MB/s   00:01
```

## pkg search - Finding a package to install with pkgng

Now that we have our repository databases up to date we can start searching the remote repository for packages to install. For most of the examples here I will be using the mysql-server 5.5 package.

#### Search for MySQL server package to install

```sh
# pkg search mysql-server
mysql-server-5.1.65            Multithreaded SQL database (server)
mysql-server-5.0.95            Multithreaded SQL database (server)
mysql-server-4.1.25            Multithreaded SQL database (server)
mysql-server-5.5.27            Multithreaded SQL database (server)
```

If you get an error like below you need to configure your **PACKAGESITE** and run `pkg update` first (see above).

```sh
# pkg search mysql-server
Unable to open remote database "repo". Try running 'pkg update' first.
```

## pkg install - Installing a package

When you have identified the package and version you wish to install you can easily install it using the `pkg install` command like this.

```sh
# pkg install mysql-server-5.5.15
Updating repository catalogue
Repository catalogue is up-to-date, no need to fetch fresh copy
The following packages will be installed:

        Installing mysql-client: 5.5.27
        Installing mysql-server: 5.5.27

The installation will require 116 MB more space

10 MB to be downloaded

Proceed with installing packages [y/N]:
mysql-client-5.5.27.txz                                                  100% 1493KB   1.5MB/s   1.5MB/s   00:01
mysql-server-5.5.27.txz                                                  100% 8691KB   8.5MB/s   6.5MB/s   00:01
Checking integrity... done
Installing mysql-client-5.5.27... done
Installing mysql-server-5.5.27...===> Creating users and/or groups.
Using existing group 'mysql'.
Using existing user 'mysql'.
 done
************************************************************************

Remember to run mysql_upgrade (with the optional --datadir=<dbdir> flag)
the first time you start the MySQL server after an upgrade from an
earlier version.

************************************************************************
```

Note: `pkg install` runs `pkg update` before attempting to install anything.

As you can see pkgng will find any dependencies it needs and install them. Before it actually does anything it will show you what it is going to do as well as how much disk space will be used and how much data will be downloaded. You can then proceed or cancel the install.

## pkg add - Installs a package from a local or remote source

The `pkg add` command is a little bit like `pkg install` but don't get them confused, they are quite different. `pkg add` requires a full path to the package file to install whether that is local or remote. It is also unable to handle dependencies automatically, as in this example.

```sh
#  pkg add http://pkgbeta.freebsd.org/freebsd-10-amd64/latest/misc/hello-2.8.txz
hello-2.8.txz                                       100%   46KB  46.0KB/s  46.0KB/s   00:00
Installing hello-2.8...missing dependency libiconv-1.14
Failed to install the following 1 package(s): http://pkgbeta.freebsd.org/freebsd-10-amd64/latest/misc/hello-2.8.txz.
```

With `pkg add` you must resolve all dependencies manually and tell it where to get the packages from.

```sh
# fetch http://pkgbeta.freebsd.org/freebsd-10-amd64/latest/All/libiconv-1.14.txz
libiconv-1.14.txz                             100% of  590 kB 1511 kBps
# pkg add libiconv-1.14.txz
Installing libiconv-1.14... done
# pkg add http://pkgbeta.freebsd.org/freebsd-10-amd64/latest/All/gettext-0.18.1.1.txz
gettext-0.18.1.1.txz                          100% of 4999 kB 5013 kBps
Installing gettext-0.18.1.1... done
# pkg add http://pkgbeta.freebsd.org/freebsd-10-amd64/latest/misc/hello-2.8.txz
hello-2.8.txz                                       100%   46KB  46.0KB/s  46.0KB/s   00:00
Installing hello-2.8... done
# hello
Hello, world!
```

## pkg info - Displaying information about installed packages

Now we have some packages installed with pkgng we can use `pkg info` to display some information about them. There are many options you can give to `pkg info` to customize the output for your needs. Such as 'Display the list of packages which depend on <pkg-name>.' (-d) and 'Display all files installed by <pkg-name>.' (-l), for the full list you should check the man page by using `pkg help info` or `man 1 pkg-info`.

#### Display all installed packages with versions and descriptions

```sh
# pkg info
mysql-client-5.5.15: Multithreaded SQL database (client)
mysql-server-5.5.15: Multithreaded SQL database (server)
nInvaders-0.1.1: The nIvaders game is a Space Invaders clone for ncurses
```

#### Display package dependencies

```sh
# pkg info -d mysql-server-5.5.19
mysql-server-5.5.19 depends on:
mysql-client-5.5.19
```

#### Display all files install by package

```sh
# pkg info -l nInvaders-0.1.1
nInvaders-0.1.1 owns the following files:
/usr/local/bin/nInvaders
/usr/local/share/doc/nInvaders/README
```

## pkg query - Display information about packages how you want it

The `pkg query` command will print out information that you choose, in a format you choose, about all or selected installed packages. Very cool and useful feature for power users or for scripting. Here are some examples.

#### Query for package mysql-server and display name, version and size.

```sh
# pkg query "Package name = %n, Version = %v, Size = %sh" mysql-server
Package name = mysql-server, Version = 5.5.15, Size = 74 MB
```

#### Query all packages for name, version and size.

```sh
# pkg query -a "Package name = %n, Version = %v, Size = %sh"
Package name = mysql-client, Version = 5.5.15, Size = 36 MB
Package name = mysql-server, Version = 5.5.15, Size = 74 MB
Package name = nInvaders, Version = 0.1.1, Size = 53 kB
```

#### Get more information for mysql-server

If you want to copy and paste this command

```sh
pkg query "
	package[%n]n
	version[%v]n
	origin[%o]n
	prefix[%p]n
	maintainer[%m]n
	comment[%c]n
	www[%w]n
	licenselogic[%l]n
	flatsize[%sh]n
	flatsizebytes[%sb]n
	orphan[%a]n
	message[%M]n
" mysql-server
```

Which will output something like this

```sh
package[mysql-server]
version[5.5.15]
origin[databases/mysql55-server]
prefix[/usr/local]
maintainer[ale@FreeBSD.org]
comment[Multithreaded SQL database (server)]
www[http://www.mysql.com/]
licenselogic[single]
flatsize[74 MB]
flatsizebytes[78348693]
orphan[0]
message[************************************************************************

Remember to run mysql_upgrade (with the optional --datadir=<dbdir> flag)
the first time you start the MySQL server after an upgrade from an
earlier version.

************************************************************************]
```

There is more you can do with `pkg query` so dive into the man page and get playing.

## pkg which - Pkgng can tell you which package created that file

When packages are installed they can scatter files all over the place. You might be told which file went were when installing the packages, but the chances are you wont know which files originated from which package. Unless you look at the plist or use `pkg info -la`, but this is messy just to grep for some file. Pkgng provides `pkg which`, you can run it against any file to see if it came from a package and if so which one.

#### Where did /usr/local/lib/mysql/libmysqlclient.a come from?

```sh
# pkg which /usr/local/lib/mysql/libmysqlclient.a
/usr/local/lib/mysql/libmysqlclient.a was installed by package mysql-client-5.5.15
```

## pkg backup - Backing up the local package database

As mentioned before pkgng uses a database to keep track of the packages and to store information about them. As you can imagine if this file gets removed or corrupted you could find yourself in a mess. Pkgng gives you a way of backing up and restoring this database.

#### Backing up the local package database to a file.

```sh
# pkg backup -d /tmp/out
Dumping database...done
# file /tmp/out.txz
/tmp/out.txz: xz compressed data
# tar -tvf /tmp/out.txz
-rw-r--r--  0 root   wheel   12584 Jan  1  1970 mysql-client-5.5.15.yaml
-rw-r--r--  0 root   wheel   17557 Jan  1  1970 mysql-client-5.5.15.mtree
-rw-r--r--  0 root   wheel   24186 Jan  1  1970 mysql-server-5.5.15.yaml
-rw-r--r--  0 root   wheel       0 Jan  1  1970 mysql-server-5.5.15.mtree
-rw-r--r--  0 root   wheel     799 Jan  1  1970 nInvaders-0.1.1.yaml
-rw-r--r--  0 root   wheel       0 Jan  1  1970 nInvaders-0.1.1.mtree
```

#### Restoring the local package database from backup

```sh
# pkg backup -r /tmp/out.txz
Restoring database...done
```

## pkg create - Creating packages using pkgng

You can create packages using `pkg create` from your local binaries, these packages can then be distrusted to other machines and installed using the `pkg add` command.

```sh
# pkg create -a
Creating package for mysql-client-5.5.15
Creating package for mysql-server-5.5.15
Creating package for nInvaders-0.1.1
# ls
mysql-client-5.5.15.txz
mysql-server-5.5.15.txz
nInvaders-0.1.1.txz
```

## pkg version - Installed versions of packages

<del datetime="2012-02-14T11:45:22+00:00">You can compare the versions of packages you have installed against the remote version using the <strong>pkg version</strong> command. If you and playing along at home this is the time to update your <strong>pkg.conf</strong> to use the newer repository, (see above).</del>

#### <del datetime="2012-02-14T11:45:22+00:00">First you should update your local copy of the remote repository database</del>

**Edit:** You can use `pkg version` to see and compare the versions of packages you have installed. According to the pkgng wiki the `pkg version` command is the same as `pkg_version`, this means that package versions are compared with your local ports Makefiles or index file.

#### Then run the pkg version command to see packages that need upgrading

```sh
# pkg version -v
mysql-client                       <   needs updating (port has 5.5.20)
mysql-server                       <   needs updating (port has 5.5.20)
nInvaders                          =   up-to-date with port
```

## pkg updating - Search /usr/ports/UPDATING for upgrading notes

It is important to check the **/usr/ports/UPDATING** file for any changes to a package that might require extra actions or things to be aware of before you upgrade it. `pkg updating` provides a way of searching this file using the `pkg` CLI. Running `pkg updating` with no options will print out the entire file, but you can add some basic filters to help you find what you're looking for.

#### For more details on pkg updating usage see the man page

```sh
# pkg help updating
```

#### Show messages after 01/01/2011

```sh
# pkg updating -d 20110101
20110319:
  AFFECTS: users of databases/mysql55-client
  AUTHOR: ale@FreeBSD.org

  The shared library version of the client library was increased to reflect
  ABI changes, and avoid compatibility problems with the client library
  in MySQL 5.1, so client programs that use the 5.5 client library should
  be recompiled against the 5.5.10 client library.
  This can be achieved with:

  # portmaster -r mysql-client-5.5
  or
  # portupgrade -fr mysql-client-5.5
```

## pkg upgrade - Upgrading packages with pkgng

Pkgng can take advantages of packages that are upgrade aware, by executing the provided PRE-UPGRADE and POST-UPGRADE scripts. If the package is not upgrade aware it will simply just do a pkg delete and pkg install.

#### Upgrade all outdated packages

```sh
# pkg upgrade
The following packages will be upgraded:
	Upgrading mysql-client: 5.5.15 -> 5.5.19
	Upgrading mysql-server: 5.5.15 -> 5.5.19

the upgrade will require 4 MB more space
9 MB to be downloaded

Proceed with upgrading packages [y/N]: y
http://repos.etoilebsd.net/9-amd64-20111222/All/mysql-client-5.5.19.txz       100% 1518KB   1.5MB/s   1.5MB/s   00:00
http://repos.etoilebsd.net/9-amd64-20111222/All/mysql-server-5.5.19.txz       100% 8395KB   8.2MB/s   6.8MB/s   00:01
Checking integrity... done
Upgrading mysql-client from 5.5.15 to 5.5.19... done
Upgrading mysql-server from 5.5.15 to 5.5.19...==> You should manually remove the "mysql" user.
===> Creating users and/or groups.
Using existing group 'mysql'.
Using existing user 'mysql'.
 done
```

As with `pkg install` you will be shown the actions that will be taken, how much additional disk space will be used and how much data needs to be downloaded. You can then continue or abort if you see something you don't like.

## pkg delete - Removing an installed package

Deleting a package with pkgng is pretty self explanatory, but as always do check the man page as it does't have some cool options, such as regular expressions.

#### Removing MySQL server

```sh
# pkg delete mysql-server
The following packages will be deinstalled:
	mysql-server-5.5.19

The deinstallation will save 79 MB

Proceed with deinstalling packages [y/N]: y
Deinstalling mysql-server-5.5.19...==> You should manually remove the "mysql" user.
 done
```

You will have a chance to abort if you change your mind and the usual pre-operation information is displayed. Any additional manual operations that you need to do will be printed at the end. Such as here, manually removing the "mysql" user.

## pkg audit - built in portaudit

Security vulnerabilities are always being found and reported, so it is important to have a clean way of checking your installed packages against a currently list of known problems. Normally for this we would install portaudit, have it email us problems from periodic and warn us when trying to installing a port with known problems.

Guest what, pkgng has this feature built it. Lets look at a basic example of it in use.

#### Running pkg audit for the first time.

```sh
# pkg audit
pkg: unable to open audit file, try running pkg audit -F first
```

Ok so we need to download the audit database file first.

```sh
# pkg audit -F
http://portaudit.FreeBSD.org/auditfile.tbz               100%   76KB  37.8KB/s  55.6KB/s   00:02
openssl-1.0.0_9 is vulnerable
OpenSSL -- CMS and S/MIME Bleichenbacher attack

WWW: http://portaudit.FreeBSD.org/60eb344e-6eb1-11e1-8ad7-00e0815b8da8.html


1 problem(s) in your installed packages found.
```

If your familiar with portaudit then this command will make you feel right at home.

## Pkg 1.0 is out now! The betas and RCs are over, say hello to the first release!

For more information see the <a href="http://wiki.freebsd.org/pkgng" title="pkgng - FreeBSD Wiki" target="_blank">pkgng - FreeBSD Wiki</a>.