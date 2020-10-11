---
id: 759
title: 'show running-config without more'
date: 2012-03-20T01:16:47+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=759
permalink: /cisco/show-run-without-more.html
categories:
  - Cisco
tags:
  - cisco
  - cisco devices
---
# Cisco: show running-config without more

When executing the **show running-config** (show run) command on Cisco ISO, the output will be paged through one screenful at a time. This is useful as Cisco configs can be very long and can have thousands of lines. It would be impractical to dump the whole config to screen and expect the admin to rely on their scroll buffer. However admins like to have freedom to choose, to execute **show run with out more** you can use this method.

#### Disable more

```
# terminal length 0
# show running-config
```

If you are not **enable** you can test your **terminal length** setting with a command like **show version**.

The **terminal length** command is where you set the number of lines to display per screen, 0 means no limit, however you could use this setting to show, say, 5 lines at time.

Once this command has been executed it will disable paging (more) for all output in the current session. Next time you login more paging will be enabled again.

#### Terminal length values

```
terminal length ?
  <0-512>  Number of lines on screen (0 for no pausing)
```