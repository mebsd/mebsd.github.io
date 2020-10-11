---
id: 1139
title: 'ifconfig physical link down'
date: 2012-12-21T12:21:21+00:00
author: Jake
layout: default
guid: https://mebsd.com/?p=1139
permalink: /make-build-your-freebsd-word/ifconfig-physical-link-down.html
parent: Network
grand_parent: freebsd
image: /wp-content/uploads/2012/12/hartingrj-industrial_-rj45-700.jpg
categories:
  - Build Your FreeBSD World
  - Configure FreeBSD
  - Network Configuration
tags:
  - freebsd 9.0
  - ifconfig
  - network
---
# FreeBSD ifconfig shutdown: Physical link down

When you **ifconfig down** an interface in FreeBSD it is virtually disabled, that is to say, it stops passing traffic but the physical link (layer 1) remains up. The absence of the physical link down is a shame. There are some good uses for this feature, for example, if have a FreeBSD server directly connected to a (cisco) router and only want the routes announced if the port is up. Maybe you would like to force the route to fail over to a backup server by running a command on the FreeBSD server. With the following patch this is now possible for **em(4)** interfaces.

The patch I've tested is for use with **em(4)** interfaces, below I have also included a patch for **igb(4)** interfaces but I have not tested this one.

The patch was found on the FreeBSD lists archive, <a href="http://lists.freebsd.org/pipermail/freebsd-net/2010-December/027196.html" target="_blank">link<br /> </a> and from what I can see the credit goes to **Eugene Grosbein** for the **em(4)** patch.

The **em(4)** driver is included in the main kernel by default, but I want to keep a generic kernel so as not to break **freebsd-update(8)**. This means building just the module for the **em(4)** driver and loading it over the top on boot.

#### The Patch em(4) - FreeBSD >= 9.1

```diff
--- sys/dev/e1000/if_em.h.orig	2010-12-06 12:45:53.000000000 +0600
+++ sys/dev/e1000/if_em.h	2010-12-08 20:11:05.000000000 +0600
@@ -436,6 +436,9 @@
 	unsigned long	link_irq;

 	struct e1000_hw_stats stats;
+
+	/* Bring link down when interface goes down */
+	int		down_disables_link;
 };

 /********************************************************************************
--- sys/dev/e1000/if_em.c.orig	2013-01-03 10:52:54.062363715 +0000
+++ sys/dev/e1000/if_em.c	2013-06-28 10:31:31.880777042 +0100
@@ -498,6 +498,13 @@
 	    OID_AUTO, "fc", CTLTYPE_INT|CTLFLAG_RW, adapter, 0,
 	    em_set_flowcntl, "I", "Flow Control");

+	adapter->down_disables_link = 0;
+	SYSCTL_ADD_INT(device_get_sysctl_ctx(dev),
+	    SYSCTL_CHILDREN(device_get_sysctl_tree(dev)),
+	    OID_AUTO, "down_disables_link", CTLTYPE_INT|CTLFLAG_RW,
+	    &adapter->down_disables_link, adapter->down_disables_link,
+	    "Bring link down when interface goes down");
+
 	callout_init_mtx(&adapter->timer, &adapter->core_mtx, 0);

 	/* Determine hardware and mac info */
@@ -1296,6 +1303,7 @@
 	}

 	/* Initialize the hardware */
+	e1000_phy_hw_reset(&adapter->hw);
 	em_reset(adapter);
 	em_update_link_status(adapter);

@@ -2383,6 +2391,16 @@

 	e1000_led_off(&adapter->hw);
 	e1000_cleanup_led(&adapter->hw);
+
+	if(adapter->down_disables_link) {
+		/* Bring physical link down by powering the phy down */
+/*		e1000_power_down_phy(&adapter->hw); */
+		e1000_power_down_phy_copper(&adapter->hw);
+
+		/* Update system interface state */
+		adapter->hw.mac.get_link_status = 1;
+		em_update_link_status(adapter);
+	}
 }
```

#### The Patch em(4) - FreeBSD <= 9.0

```diff
--- sys/dev/e1000/if_em.h.orig	2010-12-06 12:45:53.000000000 +0600
--- sys/dev/e1000/if_em.h.orig	2010-12-06 12:45:53.000000000 +0600
+++ sys/dev/e1000/if_em.h	2010-12-08 20:11:05.000000000 +0600
@@ -436,6 +436,9 @@
 	unsigned long	link_irq;

 	struct e1000_hw_stats stats;
+
+	/* Bring link down when interface goes down */
+	int		down_disables_link;
 };

 /********************************************************************************
--- sys/dev/e1000/if_em.c.orig	2010-12-06 12:47:51.000000000 +0600
+++ sys/dev/e1000/if_em.c	2010-12-08 20:46:02.000000000 +0600
@@ -452,6 +452,13 @@
 	    OID_AUTO, "debug", CTLTYPE_INT|CTLFLAG_RW, adapter, 0,
 	    em_sysctl_debug_info, "I", "Debug Information");

+	adapter->down_disables_link = 0;
+	SYSCTL_ADD_INT(device_get_sysctl_ctx(dev),
+	    SYSCTL_CHILDREN(device_get_sysctl_tree(dev)),
+	    OID_AUTO, "down_disables_link", CTLTYPE_INT|CTLFLAG_RW,
+	    &adapter->down_disables_link, adapter->down_disables_link,
+	    "Bring link down when interface goes down");
+
 	callout_init_mtx(&adapter->timer, &adapter->core_mtx, 0);

 	/* Determine hardware and mac info */
@@ -1266,6 +1273,8 @@
 	}

 	/* Initialize the hardware */
+	if (!adapter->hw.phy.reset_disable)
+	        e1000_phy_hw_reset(&adapter->hw);
 	em_reset(adapter);
 	em_update_link_status(adapter);

@@ -2319,6 +2328,19 @@

 	e1000_led_off(&adapter->hw);
 	e1000_cleanup_led(&adapter->hw);
+
+	if(adapter->down_disables_link) {
+		/* Bring physical link down by powering the phy down */
+/*		e1000_power_down_phy(&adapter->hw); */
+		e1000_power_down_phy_copper(&adapter->hw);
+
+		/* Update system interface state */
+		adapter->hw.mac.get_link_status = 1;
+		em_update_link_status(adapter);
+
+		/* Reset the phy next time init gets called */
+		adapter->hw.phy.reset_disable = FALSE;
+	}
 }
```

Ok, so what this patch is doing is adding a call to **e1000\_power\_down\_phy\_copper()** when the interface is being brought down. It is also adding a new sysctl, **dev.em.0.down\_disables\_link**, for each em interface. This way you can enable physical down just for the interfaces you want.

#### Apply the patch

Now we have the patch and understand what it does lets apply it to our source tree. **First make sure /usr/src is the same version as running kernel!**

I have saved the above patch in my home directory in a file called e1000\_down\_phy.patch.

```sh
# cd /usr/src
# patch < ~/e1000_down_phy.patch
Hmm...  Looks like a unified diff to me...
The text leading up to this was:
--------------------------
|--- sys/dev/e1000/if_em.h.orig	2010-12-06 12:45:53.000000000 +0600
|+++ sys/dev/e1000/if_em.h	2010-12-08 20:11:05.000000000 +0600
--------------------------
Patching file sys/dev/e1000/if_em.h using Plan A...
Hunk #1 succeeded at 441 (offset 5 lines).
Hmm...  The next patch looks like a unified diff to me...
The text leading up to this was:
--------------------------
|--- sys/dev/e1000/if_em.c.orig	2010-12-06 12:47:51.000000000 +0600
|+++ sys/dev/e1000/if_em.c	2010-12-08 20:46:02.000000000 +0600
--------------------------
Patching file sys/dev/e1000/if_em.c using Plan A...
Hunk #1 succeeded at 498 with fuzz 2 (offset 46 lines).
Hunk #2 succeeded at 1259 (offset -14 lines).
Hunk #3 succeeded at 2410 (offset 82 lines).
done
```

If you see any failures in the output of the patch you will need to manually check what went wrong and I wouldn't recommend continuing until all issues are resolved.

#### Build patched em(4) module

```sh
# cd /usr/src/sys/modules/em
# make
```

If the make builds cleanly you will end up with a **if_em.ko** file.

#### Install the new em(4) module

Now our patched module is built we just need to install it. I want to do this in a way that doesn't touch the existing module, this is so it wont get overwritten during an update. To do this I am simply just going to give my if\_em.ko file a new name, it can be anything, I went for if\_em_custom.ko.

```sh
# cp /usr/src/sys/modules/em/if_em.ko /boot/kernel/if_em_custom.ko
# cp /usr/src/sys/modules/em/if_em.ko.symbols /boot/kernel/if_em_custom.ko.symbols
# chmod 555 /boot/kernel/if_em_custom.ko*
# chown root:wheel /boot/kernel/if_em_custom.ko*
```

#### Enable the custom em(4) module

As stated before the generic **em(4)** module is already included in the main kernel file. This means you will not be able to load the replacement module using kldload, instead you must reboot. Add the folowing into **/boot/loader.conf**, the `if\_em\_custom` part of the variable must match the name of the `.ko` file.

```sh
# echo 'if_em_custom_load="YES"' >> /boot/loader.conf
```

#### Reboot to load the new module

```sh
# reboot
```

You may see the following warning message as the kernel is loading, it is safe to ignore this.

```sh
module_register: module pci/em already exists!
Module pci/em failed to register: 17
```

#### Testing the new module

Once the system has rebooted, (did you noticed to warning message above?), we can test it. Running **kldstat** should show something thing like this.

```sh
# kldstat | grep if_em
9    1 0xffffffff81620000 6c6b8    if_em_custom.ko
```

Now check the new sysctl exists.

```sh
# sysctl -a | grep down_disables_link
dev.em.0.down_disables_link: 0
dev.em.1.down_disables_link: 0
# sysctl -d dev.em.0.down_disables_link
dev.em.0.down_disables_link: Bring link down when interface goes down
```

To enable the new feature on the interface em0

```sh
# sysctl dev.em.0.down_disables_link=1
dev.em.0.down_disables_link: 0 -> 1
#  sysctl dev.em.0.down_disables_link
dev.em.0.down_disables_link: 1
```

To enable it on boot

```sh
echo 'dev.em.0.down_disables_link=1' >> /etc/sysctl.conf
```

Now it's time to test the command.

```sh
# ifconfig em0 down
# ifconfig em0 up
```

Check the port status on the other side of the link, or look at the lights on the back of the server ;) and you should see the interface shutdown.

**N.B.** If there is absolutely no configuration on the **em(4)** interface the **ifconfig down** command will have no affect. If this is the case, just set an arbitrary IP address on the interface, or an **ifconfig up** might do it, then retest.

In case you're looking for it here is the (untested by me) patch of **igb(4)** interfaces.

```diff
--- if_igb.h.orig	2010-12-06 12:27:54.000000000 +0600
+++ if_igb.h	2010-12-06 12:29:47.000000000 +0600
@@ -450,6 +450,8 @@
 #endif

 	void 			*stats;
+	/* Bring link down when interface goes down */
+	int		down_disables_link;
 };

 /* ******************************************************************************
--- if_igb.c.orig	2010-11-30 11:14:42.000000000 +0600
+++ if_igb.c	2010-12-06 12:37:10.000000000 +0600
@@ -432,6 +432,13 @@
 	    OID_AUTO, "enable_aim", CTLTYPE_INT|CTLFLAG_RW,
 	    &igb_enable_aim, 1, "Interrupt Moderation");

+	adapter->down_disables_link = 0;
+	SYSCTL_ADD_INT(device_get_sysctl_ctx(dev),
+	    SYSCTL_CHILDREN(device_get_sysctl_tree(dev)),
+	    OID_AUTO, "down_disables_link", CTLTYPE_INT|CTLFLAG_RW,
+	    &adapter->down_disables_link, adapter->down_disables_link,
+	    "Bring link down when interface goes down");
+
 	callout_init_mtx(&adapter->timer, &adapter->core_mtx, 0);

 	/* Determine hardware and mac info */
@@ -2070,6 +2077,13 @@

 	e1000_led_off(&adapter->hw);
 	e1000_cleanup_led(&adapter->hw);
+
+	if(adapter->down_disables_link) {
+		e1000_power_down_phy(&adapter->hw);
+		igb_handle_link(adapter, 0);
+		/* Reset the phy next time init gets called */
+		adapter->hw.phy.reset_disable = FALSE;
+	}
 }
 ```