---
layout: post
title: "How to freeze capybara integration/feature spec"
date: 2014-04-28 21:05
comments: true
categories: 
---

I normally don't write feature specs a lot, because usually the process is a bit of a pain. Especially when you're trying to cover an existing legacy page with feature specs. Here's one little trick that made my work a log easier, hopefully it will help you too :)

The problem is that sometimes the spec fails and you get a vague idea why (element by selector is not found or something like that), but by the time an error is printed, the browser window is long gone (you see the browser if you use selenium capybara driver, for example). So you want to pause a test and dig around, find out what goes wrong. 

Capybara provides you with a `page` object which you can use to interact with the browser (click buttons, find elements, etc.), but I found it very inconvenient for quick poking around. Well, turns out (I didn't know this before) that you can pause a test with something like `binding.pry` or `sleep`. Then you can inspect the page under test in the browser which will be left open and not frozen (it's the separate process, so when main test process sleeps, browser is unaffected). Developer tools of modern browsers are fantastic, so it's a shame not to use them. 

Oh yes, you can also use this technique to do repeating test setup. Say, the business reports a bug to you, you want to test it, but you don't want to keep a permanent feature spec (they're dog slow). What you can do is create a temporary test that will set everything up (create three different types of accounts, two projects and what not) and just put a `binding.pry` at the end. From then you can tail `log/test.log` and test the site manually. After you're done with the bug, you just delete the test and commit the changes.

Anyway, I hope this is googlable and will save somebody some time.
