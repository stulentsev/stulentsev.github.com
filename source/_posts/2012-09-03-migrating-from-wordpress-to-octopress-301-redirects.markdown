---
layout: post
title: "Migrating from Wordpress to Octopress - 301 redirects"
date: 2012-09-03 17:00
comments: true
author: Sergio Tulentsev
categories: octopress administration wordpress
keywords: octopress migration github page redirect wordpress
---

Okay, I probably can't avoid writing the obligatory migration article :) If in the 
migration process you decided to also change the domain name, then this article is for you.
If your domain name is the same, only the hosting is different, then you should set up a 
[CNAME](http://octopress.org/docs/deploying/github/). If you wiped Wordpress and installed 
Octopress in its stead, then this article is useless to you, move on. 

<!-- more -->

We want to set up a good 301 redirect, such that will take links to old blog posts and
redirect them to corresponding posts on the new domain. Redirection is good. It keeps 
search visitors happy (bots or humans).

We can implement it using `mod_rewrite`. Wordpress already requires `mod_rewrite`, so
we can count on its presence. Navigate to blog's root and look for file named `.htaccess`. 
It should have this (or similar) content:

{% codeblock %}
# BEGIN WordPress
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /blog/
RewriteRule ^index\.php$ - [L]
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /blog/index.php [L]
</IfModule>

# END WordPress
{% endcodeblock %}

We don't need all this, because we won't be using Wordpress anymore. Replace it with this
snippet

{% codeblock %}
<IfModule mod_rewrite.c>
Options +FollowSymlinks
RewriteEngine On
RewriteCond %{HTTP_HOST} ^example.com [NC]
RewriteRule ^(.*)$ http://blog.example2.com/$1 [L,R=301]

RewriteCond %{HTTP_HOST} ^www.example.com [NC]
RewriteRule ^(.*)$ http://blog.example2.com/$1 [L,R=301]
</IfModule>
{% endcodeblock %}

In short, these two rules take any URL from the old domain (with or without `www`) and
replace domain name to a new one.

If your octopress blog is functioning on the new domain, no more actions are required. It
should work already.