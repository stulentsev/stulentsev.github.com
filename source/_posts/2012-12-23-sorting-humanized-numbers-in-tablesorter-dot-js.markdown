---
layout: post
title: "Sorting humanized numbers in tablesorter.js"
date: 2012-12-23 17:48
comments: true
categories: javascript clientside
keywords: coffeescript javascript tablesorter build custom parser
---

[Tablesorter](http://tablesorter.com/docs/) is a jQuery plugin for sorting tables. It appeared first in a couple of "X jquery table sorting plugins" blog posts, so I went with it when I needed to sort a table a couple of days ago.

It works pretty well out of the box and it even lets you customize some bits. One of those bits is column parser: if you have special data in a column, you need a special way to sort it.

Here's [an official example](http://tablesorter.com/docs/example-parsers.html)

``` javascript
// add parser through the tablesorter addParser method 
$.tablesorter.addParser({ 
    // set a unique id 
    id: 'grades', 
    is: function(s) { 
        // return false so this parser is not auto detected 
        return false; 
    }, 
    format: function(s) { 
        // format your data for normalization 
        return s.toLowerCase().replace(/good/,2).replace(/medium/,1).replace(/bad/,0); 
    }, 
    // set type, either numeric or text 
    type: 'numeric' 
}); 
 
$(function() { 
    $("table").tablesorter({ 
        headers: { 
            6: { 
                sorter:'grades' 
            } 
        } 
    }); 
});                  
```

However, it leaves much to be desired. Like what to do when I want my parser to be auto-detected. Turns out that this `is` function is a predicate. It accepts a string and decides whether it can be handled by this parser. Then the `format` function will make conversion to a normalized textual/numeric form.

In my case I have a table with a lot of numbers. And those numbers are in humanized form. That is, instead of `121738` I output `121.74k`. This format is not properly sortable as either number or text, so I wrote a parser.

``` javascript
$.tablesorter.addParser({
  id: 'humannum',
  is: function(s) {
    return /^[\d\.]+[Mk]?$/.test(s);
  },
  format: function(s) {
    var is_m = s.indexOf('M') !== -1;
    var is_k = s.indexOf('k') !== -1;
    var fl = parseFloat(s);
    var res = fl;
    if (is_m) {
      res = fl * 1000000;
    } else if (is_k) {
      res = fl * 1000;
    }
    return res.toString();
  },
  type: 'numeric'
});
```

It basically checks if a string consists of a number followed by an optional "M" or "k" and, if that's the case, converts it to a "full" number. 

I wish this was in the official doc.