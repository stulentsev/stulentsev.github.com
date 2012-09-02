---
comments: true
date: 2010-08-24 13:00:13
layout: post
slug: jquery-chaining
title: jQuery chaining
wordpress_id: 189
categories:
- Web development
---

I am beginning to fall in love with jQuery. Screw you, [bicycle inventors](http://vkontakte.ru)! :-)

``` javascript
function next_step() {
    // note how elegant and self-explanatory this code is.
    var cl = 'step_selected';
    $('.' + cl).removeClass(cl).next().addClass(cl);

    // Also look at previous version of the code,
    // before I remembered about chaining methods.
    
    // var li = $('.step_selected');
    // li.removeClass('step_selected');
    // li.next().addClass('step_selected');

    // Not as sexy, right? :-)
}
```