---
comments: true
date: 2012-04-01 23:14:50
layout: post
slug: define-module-programmatically
title: Define module programmatically
wordpress_id: 354
categories: ruby metaprogramming
---

``` ruby
module Foo
end
```

Given a module `Foo`, how do you define a nested module `Bar`?

``` ruby
module Foo
    module Bar
    end
end
```

There's a number of ways. First, most obvious one is to eval a string.

``` ruby
module Foo
end

name = 'Bar'

Foo.class_eval <<RUBY
  module #{name}
  end
RUBY

Foo::Bar # => Foo::Bar
```

While this certainly works and gets job done, it has some flaws. First, it's a string, so some editors and IDEs will get confused and lose coloring/completion. Second, there's no validation on module name. In best case, you'll get compiler error. In worst case, you'll get hard to track bugs.

``` ruby
module Foo
end

bar = Module.new
Foo.const_set(:Bar, bar)

Foo::Bar # => Foo::Bar
```

This one is better. You clearly state that you're going to set a constant, and module name is pretty restricted.

Are there other ways to do this?