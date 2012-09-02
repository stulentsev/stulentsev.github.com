---
comments: true
date: 2008-12-19 10:37:48
layout: post
slug: case-sensitivity-and-tools
title: Case sensitivity and tools
wordpress_id: 61
categories:
- Rants
---

I want to say again that decision to make identifiers case-insensitive is one of the worst design decisions a language author can make. Along with that goes "declare on first usage" decision, but more on this later.

Why is it bad? Because you either have to have proper development tools that support this decision, or assume high professionalism of the developers, letting them use conventional tools. Good professional developer will be consistent with casing of identifiers, because he sticks to some naming convention and case means something there (this is wild assumption, but I believe it is true, hehe).

Recently I had to fix some ancient program. It is written in Turbo Pascal 5. The IDE is even more useless than even Windows Notepad (can you imagine this?), that's why I used [Notepad++](http://notepad-plus.sourceforge.net/uk/about.php) to edit sources and launch compilation script.

In some recent versions it acquired so-called "smart highting". This feature highlights all occurrences of a word when you select one of them. Very handy. BUT! it is case sensitive.

[![image](http://software.tulentsev.com/images/uploads/2008/12/image-thumb3.png)](http://software.tulentsev.com/images/uploads/2008/12/image3.png)

Take a look at the screenshot. You can see two occurrences of _valor_ highlighted with green color, but another occurrence is not highlighted, it has different casing. This makes analysis of this source code somewhat harder.

Well, we could blame two sides here: lack of "case sensitivity" options for Notepad++ and this design decision of Niklaus Wirth. I tend to do the latter :-)
