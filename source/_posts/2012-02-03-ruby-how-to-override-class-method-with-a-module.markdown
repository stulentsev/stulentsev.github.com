---
comments: true
date: 2012-02-03 17:09:00
layout: post
slug: ruby-how-to-override-class-method-with-a-module
title: 'Ruby: how to override class method with a module'
wordpress_id: 298
tags:
- metaprogramming
- ruby
---

This seems to be a popular interview question. It indeed requires advanced knowledge of ruby.
> You have a class with a class method. Write a module that, when included, will override that class method.

### Explanation of the problem

Now classic way of mixing in class methods is this (and it doesn't solve the problem, of course).

``` ruby
module FooModule
  def self.included base
    base.extend ClassMethods
  end

  module ClassMethods
    def bar
      puts "module"
    end
  end
end

class Klass
  include FooModule

  def self.bar
    puts 'class'
  end
end


Klass.bar #=> class
```

When modules are included or extended into a class, its methods are placed right above this class' methods in inheritance chain. This means that if we were to call `super` in that class method, it would print "module". But we don't want to touch original class definition, we want to alter it from outside.

### So, can we do something?

Good for us, ruby has a concept of ["open classes"](http://www.google.ru/search?ix=hca&sourceid=chrome&ie=UTF-8&q=ruby+open+classes). This means that we can change virtually everything in the app, even some 3rd-party libraries. Every class can "opened" and new methods can be added to it, or old methods can be redefined. Let's look how it works.

``` ruby
class Klass
  def self.bar
    puts 'class'
  end
end

class Klass
  def self.bar
    puts 'class 2'
  end
end

Klass.bar #=> class 2
```

The second class definition does not overwrite previous one, it opens and alters it. In this case, it happened to define a method with the same name. This resulted in old method being overwritten by the new one. This works with any classes, even base library classes.

``` ruby
puts [1, 2, 3].to_s #=> [1, 2, 3]

class Array
  def to_s
    "an array: #{join ', '}"
  end
end

puts [1, 2, 3].to_s #=> an array: 1, 2, 3
```

Or the same code can be rewritten as

``` ruby
puts [1, 2, 3].to_s #=> [1, 2, 3]

Array.class_eval do
  def to_s
    "an array: #{join ', '}"
  end
end

puts [1, 2, 3].to_s #=> an array: 1, 2, 3
```

### Applying the knowledge

Let's start with simpler things, like overriding an instance method.

``` ruby
class Klass
  def say
    puts 'class'
  end
end

module FooModule
  def self.included base
    base.class_eval do
      def say
        puts "module"
      end
    end
  end
end


Klass.send(:include, FooModule)

Klass.new.say #=> module
```

Modules have a special callback that gets called every time a module is included in a class. We can use that to call class_eval on that class and redefine a method.

Replacing a class method is done in a similar way.

``` ruby
class Klass
  def self.say
    puts 'class'
  end
end

module FooModule
  def self.included base
    base.instance_eval do
      def say
        puts "module"
      end
    end
  end
end


Klass.send(:include, FooModule)

Klass.say #=> module
```
The only difference here is that we call instance_eval instead of class_eval. This can be a very confusing part. In short, class_eval creates instance methods and instance_eval creates class methods.



