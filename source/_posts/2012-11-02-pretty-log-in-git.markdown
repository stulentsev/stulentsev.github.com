---
layout: post
title: "Pretty log in Git"
date: 2012-11-02 17:11
comments: true
categories: git
keywords: git pretty log
---

Here's what default `git log` output looks like

{% img center /images/uploads/2012/11/git-ugly.png %}

While it is sure contains the information and is easily parsable by a machine, it's
not the best way to present it to a human. Luckily, the `git log` commands is quite
customizable. Try this, for example:

    git log --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset' --abbrev-commit --date=relative
    
{% img center /images/uploads/2012/11/git-pretty.png %}

You can even set make an alias for it:

    git config --global --add alias.plog "log --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset' --abbrev-commit --date=relative"
    
And then you just call

    git plog
    
Happy gitting!