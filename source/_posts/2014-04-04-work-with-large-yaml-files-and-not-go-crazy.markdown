---
layout: post
title: "How To Work with large YAML files and not go crazy"
date: 2014-04-04 20:15
comments: true
categories: 
---

Large Rails apps have large locale .yml files. Some have files so large that it is not feasible to simply open them in editor and work with them. Sure, you can edit them just fine, but you can't efficiently search them.

Say, you're implementing a new page. It has "Balance" (for example) field on it. You think: "Hm, we have several other pages with this field. Surely, it had been I18n'd before. Let's look in locale files". You open 10 kLOC en-US.yml file, start searching and find 15 entries of "balance:" string, with varying levels of indentation. What are the full names of these keys? You have no idea.

I googled for quite a while and, to my surprise, haven't found a yaml browser with search, which would show me FQN of a key. Here's my little script that I wrote to help me in navigating these ginormous files:

``` ruby
#! /usr/bin/env ruby

require 'yaml'
require 'colorize'

filename = ARGV[0]
pattern_text = ARGV[1]

unless filename && pattern_text
  puts "Usage: grep_yaml.rb filename pattern"
  exit(1)
end

pattern = Regexp.new(pattern_text, :nocase)
p pattern

hash = YAML.load_file(filename)

def recurse(obj, pattern, current_path = [], &block)
  case obj
  when String
    path = current_path.join('.')
    if obj =~ pattern || path =~ pattern
      yield [path, obj]
    end
  when Hash
    obj.each do |k, v|
      recurse(v, pattern, current_path + [k], &block)
    end
  end
end

recurse(hash, pattern) do |path, value|
  line = "#{path}:\t#{value}"
  line = line.gsub(pattern) {|match| match.green }
  puts line
end
```

Example usage (grep one of locale files of [Discourse](https://github.com/discourse/discourse) project):

{% img center /images/uploads/2014/04/04/yaml_grep.png %}

Much more comfortable, isn't it?

The source is uploaded as [gist](https://gist.github.com/stulentsev/9978365), for your copying/forking convenience. I should probably make a proper rubygem out of it. But, you know, when the immediate problem is solved, there are always more important things to work on. :/

Hope you find it useful.

