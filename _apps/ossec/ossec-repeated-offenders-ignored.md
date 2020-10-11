---
id: 468
title: 'Repeated offenders ignored'
date: 2011-12-17T12:47:34+00:00
author: Jake
layout: default
guid: http://www.mebsd.com/?p=468
permalink: /freebsd-security-hardening/solved-ossec-repeated-offenders-ignored.html
image: /wp-content/uploads/2011/12/ossec.jpg
parent: OSSEC
categories:
  - BSD Hardening
tags:
  - OSSEC
  - security
---
# OSSEC repeated offenders ignored

In OSSEC v2.6 a new feature was added, repeated offenders. This new option solves the problem of active responses having a static timeout. An attacker could repeatedly trigger an active response and would always get blocked for, say 600 seconds, then unblocked, allowed to continue attack, then blocked for 600 seconds again. Only slowing down the attack, so it would take manual intervention to block the IP for an extended period of time.

Repeated offenders seemed to be the answer to this problem, see the post about it <a href="http://dcid.me/2011/02/blocking-repeated-offenders-with-ossec" title="blocking-repeated-offenders-with-ossec" target="_blank">link</a>. So I followed the post and added a `<repeated_offenders>` into my `<active_response>` block, but although on restarting the server it would show it loaded, it was ignored.

After some testing and attempts to find answers I gave up and assumed it to be a bug, maybe the next release would fix it. Then the other day another user asked this question on the ossec mailing list, and I started testing again. I am happy to say it is now working!

Here is the thread <a href="http://groups.google.com/group/ossec-list/browse_thread/thread/c5134b2ce2d7da6f/99759ca546d91bc0?show_docid=99759ca546d91bc0" target="_blank">link</a>

**The repeated offenders configuration goes on the agent side not on the server side.**

**Problem:**  
My OSSEC setup is a server/agent configuration, I added the repeated offenders tag to my ossec.conf on the server, this is how it looked.

**Server ossec.conf:**

```xml
<active-response>
    <!-- Firewall Drop response. Block the IP for
       - 600 seconds on the firewall (iptables,
       - ipfilter, etc).
      -->
    <command>firewall-drop</command>
    <location>all</location>
    <level>6</level>
    <timeout>600</timeout>
    <repeated_offenders>30,60,120</repeated_offenders>
  </active-response>
```

When restarting the server all looked ok:

```sh
# /usr/local/etc/rc.d/ossec-hids restart
Killing ossec-monitord ..
Killing ossec-logcollector ..
Killing ossec-remoted ..
Killing ossec-syscheckd ..
Killing ossec-analysisd ..
Killing ossec-maild ..
Killing ossec-execd ..
Killing ossec-csyslogd ..
OSSEC HIDS v2.6 Stopped
Starting OSSEC HIDS v2.6 (by Trend Micro Inc.)...
Started ossec-csyslogd...
Started ossec-maild...
2011/12/17 11:59:02 ossec-execd: INFO: Adding offenders timeout: 30 (for #1)
2011/12/17 11:59:02 ossec-execd: INFO: Adding offenders timeout: 60 (for #2)
2011/12/17 11:59:02 ossec-execd: INFO: Adding offenders timeout: 120 (for #3)
Started ossec-execd...
Started ossec-analysisd...
Started ossec-logcollector...
Started ossec-remoted...
Started ossec-syscheckd...
Started ossec-monitord...
Completed.
```

But after testing the active response on one of the agents, the block would only last for 600 seconds every time, the repeated offenders was being ignored. Chris Warren, who had raised this question on the ossec-list suggested that it might be agent/server setups that do not work, and if we could test it on a local only install we might have more luck.

I had a development box a few key presses away so, I went to test it on local only install, and repeated offenders works! But why does it only work on local only installs? My first thought again, was quite incorrectly, assumed to be an issue with the code.

**Solution:**  
**It is a configuration issue!**

From when I can see, it is the **agent** part that handles repeated offenders and not the server, once this is realised the solution is simple. The `<repeated_offenders>` line does not go in the main server ossec.conf but on the **agents** `ossec.conf`, this is actually logical and means you can tweak the timeouts per agent, if you so wish.

So leaving the `<active-response>` section on the server as it was, here is a working configuration.

**Server ossec.conf (no changes needed):**

```xml
<active-response>
    <!-- Firewall Drop response. Block the IP for
       - 600 seconds on the firewall (iptables,
       - ipfilter, etc).
      -->
    <command>firewall-drop</command>
    <location>all</location>
    <level>6</level>
    <timeout>600</timeout>
  </active-response>
```

My agent `ossec.conf` is pretty small as I use `shared/agent.conf` to make configuring all my agents easier.

**Agent ossec.conf:**

```xml
<ossec_config>
  <client>
    <server-ip>xxx.xxx.xxx.xxx</server-ip>
  </client>

  <active-response>
    <repeated_offenders>30,60,120</repeated_offenders>
  </active-response>
</ossec_config>
```

Restart the agent:

```sh
# /usr/local/etc/rc.d/ossec-hids restart
Killing ossec-logcollector ..
Killing ossec-syscheckd ..
Killing ossec-agentd ..
Killing ossec-execd ..
OSSEC HIDS v2.6 Stopped
Starting OSSEC HIDS v2.6 (by Trend Micro Inc.)...
2011/12/17 12:14:38 ossec-execd: INFO: Adding offenders timeout: 30 (for #1)
2011/12/17 12:14:38 ossec-execd: INFO: Adding offenders timeout: 60 (for #2)
2011/12/17 12:14:38 ossec-execd: INFO: Adding offenders timeout: 120 (for #3)
Started ossec-execd...
Started ossec-agentd...
Started ossec-logcollector...
Started ossec-syscheckd...
Completed.
```

Now test firing the active response on the agent a few times, and repeated offenders is working.

I hoped that this could be moved to the agent.conf so that I would not need to manually send the new agent `ossec.conf` to all the agents, but it seems this is not currently supported and it must go in the agents static `ossec.conf`. Maybe in the future we can put it in `agent.conf`.

I hope this has helped some people with the same issue, this is a OSSEC feature I have been looking forward to using for a long time.