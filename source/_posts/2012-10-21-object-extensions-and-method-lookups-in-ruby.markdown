---
layout: post
title: "Object extensions and method lookups in Ruby"
date: 2012-10-21 16:30
comments: true
categories: ruby metaprogramming
---

Method lookup is an interesting topic in Ruby. For example, exactly what happens that 
produces this output from this code?

``` ruby
module Foo
  def hello
    super if defined? super
    puts "foo"
  end
end

class Bar
  include Foo

  def hello
    super if defined? super
    puts 'bar'
  end

end

bar = Bar.new
bar.hello
# >> foo
# >> bar
```

The algorithm for method lookups can be summarized in one sentence: "one step to the right,
then up". This is referring a specific visualization of objects and classes relationships,
where we place object's class to the right from the object, and place ancestor of a class
above it.

<!-- more -->

{% img /images/uploads/2012/10/lookup1.png %}

It's important to understand that instances in ruby do not hold methods, only classes do.
At the same time we're able to enhance **specific instances** with additional methods, that
other instances of the same class will not have. Look:

``` ruby
module Foo
  def hello
    super if defined? super
    "foo"
  end
end

class Bar
end

bar1 = Bar.new
bar1.extend Foo

bar2 = Bar.new

bar1.ancestors #
bar1.hello # => "foo"
bar2.hello # ~> -:17:in `<main>': undefined method `hello' for #<Bar:0x007f858210a1d0> (NoMethodError)
```

Here `bar2` instance does not have a `hello` method, while `bar1` does have it. This means
that `hello` method can't be in `bar1` instance itself (remember, instances don't contain
methods), but it also can't be in `Bar` class, because then `bar2` would also have it. So,
where is that method located?

Well, there is a special hidden class for every object *instance* in ruby. This dynamic 
class is where all those per-instance methods go. You won't be able to see it in the 
ancestors chain, ruby hides it from you. But it's there (well, technically speaking, it *appears* there when you define your first per-instance method). So, in this case the lookup goes
like this:

{% img /images/uploads/2012/10/lookup2.png %}

But what happens if `Bar` also defines a `hello` method? Which gets found and executed first?
Can you tell by looking on the pictures above?

``` ruby
module Foo
  def hello
    super if defined? super
    puts "foo"
  end
end

class Bar
  def hello
    super if defined? super
    puts 'bar'
  end
end

bar1 = Bar.new
bar1.extend Foo

bar1.hello
```

That's right it will print "bar", then "foo". Why? Because `Foo#hello` will be injected
*before* `Bar#hello` in the ancestors chain. It will go into that dynamic unique class
(which is called *eigenclass*, by the way). Here's the lookup flow:

{% img /images/uploads/2012/10/lookup3.png %}

Method from `Foo` is found first and it calls super, which is the version in `Bar` class.
This version also tries to call `super` (there isn't one), then prints "bar" and returns 
control to `Foo#hello` which prints "foo". 

Now you see, it's a simple algorithm. But it can be really puzzling if you don't know about
eigenclasses. 
