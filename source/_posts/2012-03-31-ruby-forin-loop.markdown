---
comments: true
date: 2012-03-31 07:19:36
layout: post
slug: ruby-forin-loop
title: 'Ruby: for..in loop'
wordpress_id: 349
categories: ruby programming

---

You live to learn every day. Today I discovered `for..in` loop in ruby. When I saw it in a question on [stackoverflow](http://stackoverflow.com), I was like "Hey, dude, this is ruby, not javascript!" in my head. But, apparently, it's legal ruby :)

``` ruby
arr = [1, 2, 3]

for a in arr
  puts "element #{a}"
end

#=> element 1
#=> element 2
#=> element 3  
```

You can put ranges in there also.


``` ruby
for a in (1..10)
  puts a
end
```

I usually write such loops with `.each`, but good to know there's another way.
