---
layout: post
title: "Using OSX Notification Center in your programs"
date: 2014-03-17 20:50
comments: true
categories: link
---

There is this good feature in OSX, called [Notification Center](http://en.wikipedia.org/wiki/Notification_Center) (which Apple may have stolen from Growl, who knows). It has an API, so you can post your events there. If you're using Xcode and Apple frameworks, then you can stop reading now. However, if you're programming in, say, ruby, you're in much less fortunate position. There's no official gem or library, so you are left on your own. 

But don't despair, we have you covered. There's a cocoa app called [terminal-notifier](https://github.com/alloy/terminal-notifier) that serves as a bridge between Notification Center and your app. It is accessible via command line and is quite configurable. There's also a ruby gem that wraps this tool, but the main value is that it is available in command line. Which means that you can use it from whatever language you want. For example, I use it in my stackoverflow question poller which is written in [Go](http://golang.org/).
