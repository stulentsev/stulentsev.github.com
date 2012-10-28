---
comments: true
date: 2012-03-09 08:10:18
layout: post
slug: how-to-keep-your-system-clock-synchronized-on-ubuntu
title: How to keep your system clock synchronized on Ubuntu?
wordpress_id: 309
categories: ubuntu administration
---

Your server's hardware clock isn't perfectly accurate. It may run faster or slower (in my experience it was always slower). So it is important to synchronize it every so often, or else you might encounter some unexpected bugs. There's a command in Ubuntu that synchronizes hardware clock against atomic clock servers. It's called ntpdate

``` bash
$ sudo ntpdate ntp.ubuntu.com
 9 Mar 03:59:12 ntpdate[7225]: step time server 91.189.94.4 offset 179.440440 sec
```
    
This one was three minutes behind. Could be worse, though. So, now clock is more or less accurate. To keep it this way, let's add an hourly cron job. Create a file called 'ntpdate' (for example) at '/etc/cron.hourly' with this content:

``` bash
#! /bin/sh

ntpdate ntp.ubuntu.com
```

We don't need sudo here, because these jobs are run with root privileges. Now make that file executable.

``` bash
sudo chmod +x /etc/cron.hourly/ntpdate
```

We're all set now. Come back a few days later and verify that clock doesn't deviate as much anymore.



