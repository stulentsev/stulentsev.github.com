---
layout: post
title: "How to automatically open new octopress post in editor?"
date: 2012-09-03 15:51
comments: true
categories: octopress
---

Recently I discovered [Octopress](http://octopress.org) and instead of writing another 
article on how to migrate to it from Wordpress, I decided to do something else. And, 
fortunately enough, the topic presented itself. :)

So, Octopress is not your conventional blog engine. You create new posts by running 
a command in the terminal. I'm quite comfortable with creating new posts with a rake task. 
What I didn't like is that I needed to run another command to actually open the post in my 
editor. Here's my fix to that. It patches the `new_post` command, by adding new optional 
parameter to it, `:open_in_editor`. If you pass `true`, then the post will be opened in 
TextMate. Now the creation of new post can look like this:

``` bash
rake new_post['How to automatically open new post in editor?',:open]
```
<!-- more -->

And, finally, the diff of this change:

{% codeblock Diff lang:diff http://pastie.org/pastes/4655877/download?key=fo7n7oda8y2vz43qm5s7kg Download this snippet %}
diff --git a/Rakefile b/Rakefile
index 58e1925..df8ff7e 100644
--- a/Rakefile
+++ b/Rakefile
@@ -90,10 +90,10 @@ end
 
 # usage rake new_post[my-new-post] or rake new_post['my new post'] or rake new_post (defaults to "new-post")
 desc "Begin a new post in #{source_dir}/#{posts_dir}"
-task :new_post, :title do |t, args|
+task :new_post, :title, :open_in_editor do |t, args|
   raise "### You haven't set anything up yet. First run `rake install` to set up an Octopress theme." unless File.directory?(source_dir)
   mkdir_p "#{source_dir}/#{posts_dir}"
-  args.with_defaults(:title => 'new-post')
+  args.with_defaults(:title => 'new-post', :open_in_editor => false)
   title = args.title
   filename = "#{source_dir}/#{posts_dir}/#{Time.now.strftime('%Y-%m-%d')}-#{title.to_url}.#{new_post_ext}"
   if File.exist?(filename)
@@ -109,6 +109,9 @@ task :new_post, :title do |t, args|
     post.puts "categories: "
     post.puts "---"
   end
+  if args[:open_in_editor]
+    `mate #{filename}`
+  end
 end
 
 # usage rake new_page[my-new-page] or rake new_page[my-new-page.html] or rake new_page (defaults to "new-page.markdown")
{% endcodeblock %}