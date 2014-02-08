---
layout: post
title: "Kata: Convert numbers to Roman numerals"
date: 2014-02-08 10:24
comments: true
categories: puzzle
keywords: roman number code kata arabic
---

Here is an interesting problem: write a program that converts numbers into [Roman numerals](http://en.wikipedia.org/wiki/Roman_numerals). Roman didn't use Arabic numbers. Instead they used symbols of Latin alphabet that represented different values. It's a simple system. "I" stands for 1, "V" stands for 5, "X" for 10 and so on. To represent 2 you have to use "II" (1 + 1), to represent 7 you use "VII" (5 + 1 + 1). Simple, right? Well, no. Here's a twist. 

<pre>
* 4 is "IV" (not "IIII")
* 9 is "IX" (not "VIIII")
* 40 is "XL" (not "XXXX")
* 49 is "XLIX" (not "XXXXVIIII")
</pre>

Weird, huh? Maybe they didn't like 4 identical symbols in a row, who knows. Anyway, how can we solve this?

After trying out several "smart" solutions and failing, I've come up with this simple solution: store these special cases along with normal ones and keep subtracting from the number until it's zero.

``` ruby
def romanize(number)
  reductions = {
    1000 => 'M',
    900 => 'CM',
    
    500 => 'D',
    400 => 'CD',
    
    100 => 'C',
    90 => 'XC',
    
    50 => 'L',
    40 => 'XL',
    
    10 => 'X',
    9 => 'IX',
    
    5 => 'V',
    4 => 'IV',

    1 => 'I',
  }
  
  result = ''
  
  while number > 0
    reductions.each do |n, subst|
      if number / n >= 1 # if number contains at least one of n
        result << subst  # push corresponding symbol to result
        number -= n      
        break            # break from each and start it anew 
                         # so that the largest numbers are checked first again.
      end
    end
  end
  
  result
end
```

A test:

``` ruby
test_mapping = {
  1 => 'I', 
  2 => 'II',
  3 => 'III',
  4 => 'IV', 
  5 => 'V',
  6 => 'VI',
  7 => 'VII',
  8 => 'VIII',
  9 => 'IX',
  10 => 'X',
  11 => 'XI',
  12 => 'XII',
  13 => 'XIII',
  14 => 'XIV',
  15 => 'XV',
  19 => 'XIX',
  22 => 'XXII',
  29 => 'XXIX',
  30 => 'XXX',
  40 => 'XL',
  49 => 'XLIX',
  99 => 'XCIX',
  950 => 'CML',
  2014 => 'MMXIV',
}


test_mapping.each do |k, v|
  res = romanize(k)
  sign = res == v ? '.' : 'F'
  
  puts "#{sign} #{k} => #{res.inspect}"
end
```

This simple problem took me a little bit over 2 hours to come up with this (simple) solution. Too bad, that often we, programmers, don't have time to look for simple solutions and we go for the easiest one.

Hope this helps someone.

