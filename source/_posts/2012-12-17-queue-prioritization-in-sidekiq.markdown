---
layout: post
title: "Queue prioritization in Sidekiq"
date: 2012-12-17 04:47
comments: true
categories: sidekiq
keywords: queue sidekiq priority job
---

Unlike [Resque](https://github.com/defunkt/resque), [Sidekiq](http://mperham.github.com/sidekiq/) allows you to process several queues at the same time. You can even assign priorities. Here's an example from default config:

``` yaml
:queues:
  - [often, 7]
  - [default, 5]
  - [seldom, 3]
```

If you want two queues have equal rights, just set the same priorities. However, if you omit priority parameter and define your queues like this:

``` yaml
:queues:
  - email
  - coffee
  - billing
```

Then queues will be checked in a serial manner. That is, Sidekiq will start making coffee only when all emails are sent. If a new email shows up, it will be processed before any other job. In other words, this is how Resque would handle the queues.

Choose whatever fits your task better. It's nice to have some flexibility. 