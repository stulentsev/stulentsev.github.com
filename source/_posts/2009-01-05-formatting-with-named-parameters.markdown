---
comments: true
date: 2009-01-05 10:45:17
layout: post
slug: formatting-with-named-parameters
title: Formatting with named parameters
wordpress_id: 76
categories:
- Nemerle
tags:
- format
- named formatting
- Nemerle
---

### Problems with formatting

The "official" way to format strings in .NET BCL is to use [String.Format](http://msdn.microsoft.com/en-us/library/system.string.format.aspx) method (and wrappers for it, like [Console.Write](http://msdn.microsoft.com/en-us/library/system.string.format.aspx)).

[![image](http://tech.tulentsev.com/images/uploads/2009/01/image-thumb.png)](http://tech.tulentsev.com/images/uploads/2009/01/image.png)

This is not very reliable method. You can forget to include parameter into the format string or confuse one parameter with another. And your IDE does not offer any help here.

### We want better way!

People feel that this is not right and try to invent some more convenient methods. One of which is formatting with named parameters (using names instead of ordinals). Yesterday I read [this blog post](http://haacked.com/archive/2009/01/04/fun-with-named-formats-string-parsing-and-edge-cases.aspx). There are several implementations of formatting with named parameters and author makes more reliable and efficient implementation just for fun. But you know what? It still doesn't seem right.

[![image](http://tech.tulentsev.com/images/uploads/2009/01/image-thumb1.png)](http://tech.tulentsev.com/images/uploads/2009/01/image1.png)

[Anonymous type](http://msdn.microsoft.com/en-us/library/bb397696.aspx) here is supposed to protect us from renaming issue, but it clutters the code. And again, we do not get any help from IDE (warnings about non-existing elements, highlighting, etc).

### But how can it be done even better?

Let's look at [Nemerle](http://en.wikipedia.org/wiki/Nemerle). It borrows "spliced string" syntax from languages like PHP ("hello, $username"). But it has several advantages over PHP and Perl. All expressions are resolved and typed at compile-time. And if you use [VS package](http://nemerle.org/Download), then you'll get development-time checks (on-the-fly) and highlighting.

One picture is worth a thousand words. So look at these code samples

[![image](http://tech.tulentsev.com/images/uploads/2009/01/image-thumb2.png)](http://tech.tulentsev.com/images/uploads/2009/01/image2.png)

[![image](http://tech.tulentsev.com/images/uploads/2009/01/image-thumb3.png)](http://tech.tulentsev.com/images/uploads/2009/01/image3.png)

Feel the difference! What can possibly go wrong here (except for [NullReferenceException](http://msdn.microsoft.com/en-us/library/system.nullreferenceexception.aspx), but [string.Format](http://msdn.microsoft.com/en-us/library/system.string.format.aspx) is also vulnerable to it). Also, this code is very efficient. Essentially it is transformed to a series of [string.Concat](http://msdn.microsoft.com/en-us/library/system.string.concat.aspx) or [StringBuilder.Append](http://msdn.microsoft.com/en-us/library/system.text.stringbuilder.append.aspx) calls.

P.S.: One more thousand-words-picture.

[![image](http://tech.tulentsev.com/images/uploads/2009/01/image-thumb4.png)](http://tech.tulentsev.com/images/uploads/2009/01/image4.png)



