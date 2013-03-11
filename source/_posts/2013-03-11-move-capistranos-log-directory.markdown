---
layout: post
title: "Move Capistrano's log directory"
date: 2013-03-11 14:34
comments: true
categories: deployment
keywords: capistrano rails ruby deploy deployment log logging directory folder
---

So you want to move your log directory? To a separate volume, maybe? But you are afraid that it will break too many things, like all those libraries with `#{rails_root}/log` paths hardcoded in them? Fear not, there's a simple workaround.

As you probably know, capistrano aliases your rails log dir to a shared dir outside of app folder.

```
ln -s /deploy_path/shared/log /deploy_path/current/log
```

This means that, in fact, all your logs turn up at `/deploy_path/shared/log/`. Now all we need is to symlink that dir as well!

``` bash
sudo mkdir /var/log/yourapp

# make it writable to the app
sudo chown -R youruser:yourgroup /var/log/yourapp/

# deal with existing log dir (I back it up, but you can remove it, or copy the files to 
#   the new location)
mv /deploy_path/shared/log /deploy_path/shared/log_
ln -s /var/log/yourapp /deploy_path/shared/log
```

Hope this helps :)
