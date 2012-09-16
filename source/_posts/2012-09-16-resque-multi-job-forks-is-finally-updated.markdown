---
layout: post
title: "resque-multi-job-forks is finally updated"
date: 2012-09-16 09:00
comments: true
categories: opensource
---

I was using [resque-multi-job-forks](https://github.com/staugaard/resque-multi-job-forks/)
plugin for quite a some time. What was bugging me is that the project seemed to be abandoned.
There were no new commits for 2 years. And current version of the gem had dependency on an
archaic version of Resque. So, I forked, bumped the dependency and bundled the gem. But 
then I thought: "Hey, why not share it with people?" I exchanged a couple of emails with
the owner and now I am the official owner/maintainer of said gem. This is my first 
takeover, hooray! :)

To celebrate this, today I have released version v0.3.2, which includes updated `resque`
dependency and fix for the problem when workers disappeared from resque-web after the first
fork has reached its limit.

