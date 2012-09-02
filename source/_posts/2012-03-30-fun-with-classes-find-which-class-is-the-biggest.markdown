---
comments: true
date: 2012-03-30 01:13:27
layout: post
slug: fun-with-classes-find-which-class-is-the-biggest
title: 'Fun with classes: find which class is the biggest :)'
wordpress_id: 343
tags:
- ruby
---

Did you know that in Ruby you can compare classes with 'less-than' and 'greater-than' operators? I did not (until today). Observe:




    
    
    class A
    end
    
    class B < A
    end
    
    class C < A
    end
    
    class D < B
    end
    
    A < B # false
    A > B # true
    D < A # true
    D < B # true
    D < C # nil
    C > D # nil
    





This operator returns a boolean value (as one would expect), so you can write code like this:




    
    
    puts "B inherits from A" if B < A
    





Note how it resembles the class definition syntax. I think that this is simply brilliant (not very intuitive, though. I wouldn't think of writing this code).





Now that we know this fact, let's try to sort the classes :)




    
    
    class A
    end
    
    class B < A
    end
    
    class C < A
    end
    
    class D < B
    end
    
    class E < A
    end
    
    class Class
      # we need to define the spaceship operator for classes, since it's not defined yet.
      def <=> other
        return 0 if self == other
        return -1 if self < other
        1
      end
    end
    
    klasses = [A, B, C, D, E]
    
    klasses.sort # [E, C, D, B, A]
    





Now this class hierarchy is sorted, with children first and parents last. Homework: come up with a practical application for this :)



