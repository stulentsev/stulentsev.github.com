---
comments: true
date: 2008-11-24 07:18:17
layout: post
slug: silverlight-security-error
title: 'Silverlight: Security Error'
wordpress_id: 35
categories:
- Silverlight 2
tags:
- Silverlight
---

Sometimes, you may get not-very-descriptive _System.Security.SecurityException_. Stack trace tells you that something bad happened somewhere inside _EndGetResponse_, and that's it, no more details. One possible cause is this: in a Silverlight project you may tell it to generate a temporary page that will host your application.

 

[![image](http://software.tulentsev.com/wp-content/uploads/2008/11/image-thumb.png)](http://software.tulentsev.com/wp-content/uploads/2008/11/image.png)

 

And if your application sends requests to some host (a linux virtual box, in my case), this will be the source of pain. The dynamically generated page gets loaded to Internet Explorer as [file://your_path/TestPage.html](file://your_path/TestPage.html). And you are trying to reach HTTP(S) host. This cross-scheme behavior is not allowed.

 

For more information, see [this MSDN article](http://msdn.microsoft.com/en-us/library/cc645032(VS.95).aspx) and [this forum thread](http://silverlight.net/forums/p/32198/101892.aspx).
