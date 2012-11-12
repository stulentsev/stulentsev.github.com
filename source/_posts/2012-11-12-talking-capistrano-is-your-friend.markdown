---
layout: post
title: "Talking Capistrano is your friend"
date: 2012-11-12 22:22
comments: true
categories: capistrano tweaks
keywords: capistrano tweaks ruby rails unicorn
---

I want to share with you a small tweak I came up with a couple of days ago. As some of you
know, Mac OS X includes a ton of very handy utilities. One of them is `say`. The name is 
pretty descriptive, you pass it a string and it voices it, using text-to-speech engine, 
that is built in Mac OS. Try it now:

``` bash
$ say Hello world!
```

You can use it anywhere where you can invoke shell commands. Here's my capistrano
after-hook, for example:

``` ruby
after 'deploy' do
  `say Sire, our unicorns have been deployed`
  # note the backticks. This string is roughly equivalent to
  # system "say Sire, our unicorns have been deployed"
end
```

Now I can launch my `cap deploy` (which can take minute or two) and go read Hacker News or
whatever. I will be told when deploy is finished. I'm using this for a few days already
and it still make me smile everytime. I should probably come up with more creative messages. 

Happy tweaking!