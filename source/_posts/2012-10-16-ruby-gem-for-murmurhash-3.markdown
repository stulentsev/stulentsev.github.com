---
layout: post
title: "Ruby gem for MurmurHash 3"
date: 2012-10-16 20:44
comments: true
categories: ruby
keywords: murmur murmur3 hash ruby 
---

Still driven by [that post](http://tech.tulentsev.com/2012/10/probabilistic-sets-in-redis/),
I decided to do something about it. So I went and tried to read the original paper. It was 
mind blowing. Not because it was great (I am sure it is), but because it's been a while 
since I had to read some serious math.

Then I came across this article, which could also be named "HyperLogLog for dummies": 
[Fast, Cheap, and 98% Right: Cardinality Estimation for Big Data](http://metamarkets.com/2012/fast-cheap-and-98-right-cardinality-estimation-for-big-data/).
It was much more readable. From it I learned about Murmur hash and was surprised that 
there's no ruby gem for this. Fortunately, [C++ source code](http://smhasher.googlecode.com/svn/trunk/MurmurHash3.cpp)
was available, so I decided to wrap this as a gem.

It went much smoother than I anticipated and I had working version within 3 or 4 hours.
I didn't have experience with native extensions before, someone experienced probably could 
have done it in 5 minutes, but still I consider this as an achievement! :)

So, only when I tried to push the gem to [RubyGems](http://rubygems.org), I discovered that there IS
a gem with this name: [murmurhash3](http://rubygems.org/gems/murmurhash3). The public API
is too cumbersome if you ask me, but it works and probably has less bugs than my version. 

Here's how you use it:

``` ruby
require 'murmurhash3'

s = "Hello, world!"

ints = MurmurHash3::Native128.murmur3_128_str_hash s

     #    produce a 128 bits of hash, in the form of 4 32-bit integers
ints # => [3537266143, 4048629201, 2834548068, 741500496]
```

You can turn this array of integers into a hex string like this:

``` ruby
ints.pack('L*').unpack('H*').first # => "df65d6d2d12d51f164c5f3a85066322c"
```

If you know a better/faster/more idiomatic way, please let me know :)