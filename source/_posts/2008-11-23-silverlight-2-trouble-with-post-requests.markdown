---
comments: true
date: 2008-11-23 22:04:30
layout: post
slug: silverlight-2-trouble-with-post-requests
title: 'Silverlight 2: trouble with POST requests'
wordpress_id: 30
categories:
- Problem solving
- Silverlight 2
tags:
- Silverlight
---

I am trying to make a small Silverlight control that talks to PHP server. Sometimes problems arise and I have to deal with them myself, since there's not much information on this topic in the internet. Trouble of the day is:

 

**The remote server returned an error: NotFound**

 

when trying to make simple POST request to a PHP page. I've spent half a day diagnosing this problem. And solution was found entirely by chance, because exception messages weren't helping in any way. I mean, I was always getting abovementioned error message, without any details (what host, what page, request parameters, etc). But enough of complaints, let's proceed to the solution. The troublesome code was this one:

 
    
    var _request = (HttpWebRequest)WebRequest.Create(_svcUri);
    _request.Method = "POST";
    _request.ContentType = "application/json";
    _request.Headers["sid"] = SessionID ; // <==== this line





SessionID was null at the moment and this made HttpWebRequest so pissed off that it even wouldn't send request header to the server ! And because of this, Fiddler was completely useless here. After adding necessary check, everything started working like a charm.
