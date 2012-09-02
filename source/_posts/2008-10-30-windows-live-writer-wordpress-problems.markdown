---
comments: true
date: 2008-10-30 21:37:23
layout: post
slug: windows-live-writer-wordpress-problems
title: Windows Live Writer + Wordpress problems
wordpress_id: 7
categories:
- Problem solving
tags:
- Windows Live Writer
- Wordpress
---

I installed this copy of Wordpress this evening and decided to add to Live Writer. But I've ran into problem almost immediately. While trying to auto-detect my editing style, blog name and who knows what else, Live Writer kept annoying me with this message: "**Invalid Server Response - The response to the blogger.getUsersBlogs method received from the weblog server was invalid**".

I hit Ctrl-Ctrl (very handy shortcut of Google Desktop, that summons search bar) and pasted the error text there. First, I came across [this article](http://kannan.jumbledthoughts.com/index.php/windows-live-writer-problems-with-wordpress-how-to-fix/). I followed every step in it (some of them twice :-) ), but it didn't help. So I kept searching.

Then I found [this interesting site](http://wlwplugins.com/how-to-fix-invalid-bloggergetusersblogs-method-response.php). Performing this step didn't help me either. I have installed [Fiddler](http://www.fiddlertool.com/fiddler/) and it finally shed some light on the problem. There was an error while requesting xmlrpc.php (which is used by external systems, such as WLW, to talk to Wordpress).

>   
> 
> **Warning**: include_once(/home/tulentse/public_html/blog_technical/wp-includes/class-IXR.php) [function.include-once]: failed to open stream: No such file or directory in **/home/tulentse/public_html/blog_technical/xmlrpc.php** on line **55**        
**Warning**: include_once() [function.include]: Failed opening '/home/tulentse/public_html/blog_technical/wp-includes/class-IXR.php' for inclusion (include_path='.:/usr/lib/php:/usr/local/lib/php') in **/home/tulentse/public_html/blog_technical/xmlrpc.php** on line **55**        
**Fatal error**: Class 'IXR_Server' not found in **/home/tulentse/public_html/blog_technical/xmlrpc.php** on line **108**

I navigated to that file and saw that the name is in lower case (and case matters on Unix-based systems). It was joint effort of me, who checked "Convert filenames to lower case" option and Total Commander who did what I told him.

Now everyone's happy :-)
