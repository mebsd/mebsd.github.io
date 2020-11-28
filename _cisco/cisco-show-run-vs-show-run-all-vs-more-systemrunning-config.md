---
title: 'Ways to view configuration'
author: Jake
layout: default
permalink: /cisco/cisco-show-run-vs-show-run-all-vs-more-systemrunning-config.html
categories:
  - Cisco
tags:
  - cisco
  - cisco devices
  - network
---
# Cisco show run vs show run all vs more system:running config

When viewing the running config on a Cisco IOS there are a few methods. The first is the well known `show run`, this will give you most of the configuration. However `show run` does hide the default configuration lines and encrypted keys. If you don`t want your configuration filtered there are some other methods.  
  
Below are examples of the other methods you can use to view the current running configuration.

#### Show running configuration, unfiltered.

```sh
# show run all
```


This is very much like `show run`, but it will also print all the default configuration options that are sometimes hidden depending on the IOS version.

#### Show configuration as it is in memory.

If you want to see your config as it is in memory, without encrypting and stuff like that you can use this command.

```sh
# more system:running config
```


It's also worth noting that if you backup/audit you Cisco configs with `rancid`, the newer version of `randic`, `rancid 2.3.6` or newer, has changed the command it uses to get the configuration. The command it uses is `more system:running config`, the older versions simply use `show run`. So if you administrate your Cisco users and restrict commands to the rancid user via a `tacacs+` server make sure you give the rancid user access to the `more` command.