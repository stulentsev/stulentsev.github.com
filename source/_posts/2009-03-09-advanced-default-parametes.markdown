---
comments: true
date: 2009-03-09 23:20:29
layout: post
slug: advanced-default-parametes
title: Advanced default parameters
wordpress_id: 162
tags:
- ruby
- scripting
---

Today I was quite amazed by one of Ruby features. It is about default values of method parameters. For example you can do something like this:

``` ruby
def get_current_actions(project_id, status_id = params[:status_id] || DEFAULT_STATUS_ID)    
    # implementation goes here
end
```
The code is saying basically this: "if status_id is not passed explicitly, try to take its value from <strong>params</strong> array. If it doesn't contain specified key, then fall back to a constant". This feature (as almost all the rest of Ruby magic) made avaiable by Ruby's nature: it is interpreted language. This type of code is totally unusual to guys like me, who come from the world of static typing and compiled languages. But I think I'm gonna get used to it :-)