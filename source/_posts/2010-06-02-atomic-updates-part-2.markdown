---
comments: true
date: 2010-06-02 11:12:41
layout: post
slug: atomic-updates-part-2
title: Atomic updates, part 2
wordpress_id: 184
categories:
- Database
tags:
- MongoDb
---

Let's review another common scenario: posts and votes. Mongo's approach would be to store vote count in the post itself (caching) and keep voters also in the post, in an array field. Let's assume we want to register a new vote on post. This operation consists of three steps:

>
1. ensure that the voter hasn't voted yet, and, if not,
2. increment the number of votes and
3. add the new voter to the array.

MongoDB's query and update features allows us to perform all three actions in a single operation. Here's what that would look like from the shell:


``` javascript
// Assume that story_id and user_id represent real story and user ids.
db.stories.update({_id: story_id, voters: {'$ne': user_id}},        
                  {'$inc': {votes: 1}, '$push': {voters: user_id}});
```


What this says is "get me a story with the given id whose voters array does not contain the given user id and, if you find such a story, perform two atomic updates: first, increment votes by 1 and then push the user id onto the voters array."

This operation highly efficient; it's also reliable. The one caveat is that, because update operations are "fire and forget," you won't get a response from the server. But in most cases, this should be a non-issue.


quote from [MongoDB site](http://www.mongodb.org/display/DOCS/MongoDB+Data+Modeling+and+Rails).

How would you implement this in your regular SQL database?

