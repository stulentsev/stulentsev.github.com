---
comments: true
date: 2010-06-01 20:01:26
layout: post
slug: mongoid-and-atomic-increment
title: MongoId and atomic increment
wordpress_id: 179
categories:
- Database
tags:
- MongoDb
- MongoId
---

I am starting to learn this new piece of technology, the MongoDB. It has many sweet features. However, using Ruby driver directly is not too comfortable. Plus we're all hooked to ORM sweeteners. So people started developing ORM libraries for MongoDB and Ruby ([MongoId](http://mongoid.org), [MongoMapper](http://github.com/mongomapper/mongomapper) to name a few).

I have decided to try MongoId first. It seems to be a quite good library. However, it still has gaps in documentation. Today I was finding out how to perform an atomic increment of a field. In SQL world this would look like:

    
    
    update users set spam_count = spam_count + 1;
    



Mongo Ruby driver supports this kind of operation, but MongoId doesn't. So I was trying to get down to the driver. And this is what I couldn't easily find in the docs. If you're like me, I'll save you a couple of hours. Here it is:

    
    
    # assume we know object id already
    User.collection.update({'_id' => '4c05848e45760182b5000001'}, {'$inc' => {'spam_count' => 1}})
    
