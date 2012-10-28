---
layout: post
title: "Probabilistic sets in Redis (almost magic)"
date: 2012-10-16 00:18
comments: true
categories: redis data-structures
keywords: redis set sketch cluster hyperloglog
---

A very interesting topic was posted today in Redis google group, called 
["Set Sketch Implementation"](https://groups.google.com/forum/#!msg/redis-db/8Zt_6hJo09k/FJCnrr9OSikJ).

> One nice application of set sketches is efficient storage and retrieval of analytics-type data: if you want to count the number of distinct users who have performed specific actions in your system ("visited page X", "pressed button Y", "followed user Z", etc.) you're quickly overwhelmed with the amount of memory needed to store even bit vectors representing all events for all users. Bloom counters are sometimes used for this sort of thing, but they still require a significant investment in space and the results of individual counters can't be easily/efficiently combined.

> HyperLogLog is a good alternative when you don't need exact counts: with a small, fixed amount of space per counter, it can yield good estimates of set cardinality - usually to within 2% of the actual count. In addition, the counters can be combined and queried at little extra cost, so that you can compute estimates to queries like "how many users visited page X and pressed button Y" or "how many users follow either user W or user Z" in real time. All of the operations take O(1) time and space in the cardinality of the sets being represented. Adding an element to a set sketch is particularly fast, essentially just the cost of applying a hash function to the element.

Now that I know about this technology, I desperately want it. I could really use that
because my current app has a lot of sets, and some of them can get pretty large. Being
able to use constant size is a huge relief, because you can correctly estimate amount of
memory taken by Redis. And being able to combine them (UNION/INTER) is priceless. 

The only thing that is not clear to me is how this will play along with upcoming Redis Cluster.
Single key operations are OK, of course, but what about multiple key ops?

I really hope that Salvatore will not reject this functionality :)