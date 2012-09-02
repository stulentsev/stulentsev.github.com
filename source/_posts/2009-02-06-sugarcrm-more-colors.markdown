---
comments: true
date: 2009-02-06 15:24:41
layout: post
slug: sugarcrm-more-colors
title: 'SugarCRM: More colors!'
wordpress_id: 120
categories:
- SugarCRM
- Web development
tags:
- Calendar
- color
- PHP
- SugarCRM
---

When I was implementing [previous issue](http://software.tulentsev.com/2009/02/sugarcrm-really-shared-calendar/), my manager asked me: "Can we also mark meetings with colors, corresponding to users?". This way it becomes somewhat easier to identify originator of an event. "Yeah, it would be cool, but it can be tricky and difficult to implement", I said, rejecting the feature request. I said so just to save my ass from potentially frustrating project. Just the day before Marcelo had told me that he has a friend who was working with SugarCRM for a year and he hadn't quite enjoyed the experience.

But later at home I was thinking about it and decided that it may not be as hard as it looks. We can divide the task into two subtasks:
	
  * provide a unique color for each user
  * use this color when generating markup for an event

The latter subtask was quite easy to implement, I already knew a place where the markup was generated. I was more scared by the former one. If done in "enterprisey" way, it could involve new property in User entity, new configuration screens, database alterations, etc. But there is a solution almost as good as abovementioned and much more simple. Let's define a function that maps user name to color. md5() is a good candidate :-)

``` php
function get_color_by_name($name)
{
    $html_color_string = substr(md5($name), 0, 6);
    return $html_color_string;
}
```

I also used RGB<=>HSV transformation functions, because I wanted to get lighter colors. Initially I wanted to highlight event's name with the color, but it turned out that text is not always readable. I decided not to write functions that calculate good contrasting colors and put a small color rectangle instead of highlight. This rectangle is essentially a span with several &nbsp; 's as a body.

So, final result looks like this:[![sugar_colors](http://software.tulentsev.com/images/uploads/2009/02/sugar_colors.png)](http://software.tulentsev.com/images/uploads/2009/02/sugar_colors.png)

Here is [complete patch](http://software.tulentsev.com/images/uploads/2009/02/colors.patch) (it has too big lines to look good on this page)
