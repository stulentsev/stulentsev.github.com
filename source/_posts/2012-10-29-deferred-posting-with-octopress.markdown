---
layout: post
title: "Deferred posting with Octopress"
date: 2012-10-29 10:42
comments: true
categories: wordpress octopress
---

One of the powerful things that Wordpress can do and Octopress can't, is deferred posting 
(or scheduled blogging, whatever). I never really missed it when I was using Wordpress, so
I switched to Octopress without hesitation. But now I could use that feature. In case you 
don't know, deferred posting allows you to write a post beforehand and schedule its release
to some moment in future.

{% img center /images/uploads/2012/10/wp-datepicker.png %}

This functionality is kinda trivial if you use dynamic request processing and a database 
(like wordpress does). But with static sites it's not at all that easy. So before 
implementing something myself, I decided to google for other works on this topic. Here's 
a good article that I've found: [Synced and scheduled blogging with Octopress](http://instant-thinking.de/2012/08/03/synced-and-scheduled-blogging-with-octopress/#fnref:3).
It also contains some links to other articles.

In short, people write a daemon (or scheduled script) that checks your drafts from time to 
time. If a draft is due, it is copied to `_posts` directory and generate/deploy sequence
is initiated.

This, obviously, requires a computer that is always on and has actual content for the site.
I don't really like the idea of a daemon, but, I guess, this is a price you pay for using 
"blog engine for hackers". 

Could this be implemented as an external web service? Is there an opportunity for a 
profitable startup? :)
