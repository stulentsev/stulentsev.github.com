---
comments: true
date: 2012-02-03 16:12:54
layout: post
slug: mongoid-db-system-namespaces-queries
title: Mongoid, db.system.namespaces queries
wordpress_id: 295
tags:
- MongoDb
- MongoId
---

Recently I faced some issues with [Mongoid](http://mongoid.org) when upgrading my Rails app from REE+Passenger to MRI 1.9.3+Unicorn.

There are some Resque workers in the background. After some deploy they started to consume a ton of traffic from MongoDB. After some investigation, I found that they heavily read `system.namespaces` collection. I tried upgrading to latest versions of `mongoid`(2.4.3) and `mongo`(1.5.2) to no avail. This does not happen with normal unicorn workers. This also does not happen if I downgrade `mongoid` to 2.0.1.

I am still not sure what's happening here. I'll update this post when I discover something.



