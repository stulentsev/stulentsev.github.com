---
comments: true
date: 2012-03-09 16:02:46
layout: post
slug: deploying-with-sinatra-capistrano-unicorn
title: Deploying with Sinatra + Capistrano + Unicorn
wordpress_id: 323
tags:
- capistrano
- ruby
- sinatra
- unicorn
---

Today we'll be deploying a simple Sinatra app with Capistrano, using Unicorn as our web server. First things first: let's think of a stupid name for this project. What about "sincapun"? Any objections? Good, let's proceed.

``` bash
mkdir sincapun
cd sincapun
```

Minimal runnable version:

``` ruby
# Gemfile
gem 'sinatra'


# sincapun.rb
require 'sinatra'

get '/' do
    "Hello world"
end
```

Let's see if we can run it...

``` bash
$ bundle exec ruby sincapun.rb 
[2012-03-09 16:04:17] INFO  WEBrick 1.3.1
[2012-03-09 16:04:17] INFO  ruby 1.9.3 (2012-02-16) [x86_64-darwin11.3.0]
== Sinatra/1.3.2 has taken the stage on 4567 for development with backup from WEBrick
[2012-03-09 16:04:17] INFO  WEBrick::HTTPServer#start: pid=14871 port=4567
```

So far, so good. Now let's convert it to modular app and create a proper rackup file.

``` ruby
# sincapun.rb
require 'sinatra'

class Sincapun < Sinatra::Base
  get '/' do
    "Hello world"
  end
end

# config.ru
require './sincapun'
run Sincapun
```

At this point our app is runnable by every Rack-compatible server (thin, unicorn, ...). Let's add some Capistrano to it.

``` ruby
# Gemfile
source :rubygems

gem 'sinatra'
gem 'unicorn'
gem 'capistrano'
```

``` bash
$ bundle install
Using highline (1.6.11) 
Using net-ssh (2.3.0) 
Using net-scp (1.0.4) 
Using net-sftp (2.0.5) 
Using net-ssh-gateway (1.1.0) 
Using capistrano (2.11.2) 
Using kgio (2.7.2) 
Using rack (1.4.1) 
Using rack-protection (1.2.0) 
Using raindrops (0.8.0) 
Using tilt (1.3.3) 
Using sinatra (1.3.2) 
Using unicorn (4.2.0) 
Using bundler (1.0.22) 
Your bundle is complete! Use `bundle show [gemname]` to see where a bundled gem is installed.

$ capify .
[add] writing './Capfile'
[add] making directory './config'
[add] writing './config/deploy.rb'
[done] capified!
```

Let's improve our capistrano config a little bit.


``` ruby
# config/deploy.rb
# We're using RVM on a server, need this.
$:.unshift(File.expand_path('./lib', ENV['rvm_path']))
require 'rvm/capistrano'
set :rvm_ruby_string, '1.9.3'
set :rvm_type, :user

# Bundler tasks
require 'bundler/capistrano'

set :application, "sincapun"
set :repository,  "git@github.com:stulentsev/sincapun.git"

set :scm, :git

# do not use sudo
set :use_sudo, false
set(:run_method) { use_sudo ? :sudo : :run }

# This is needed to correctly handle sudo password prompt
default_run_options[:pty] = true

set :user, "myname"
set :group, user
set :runner, user

set :host, "#{user}@myhost" # We need to be able to SSH to that box as this user.
role :web, host
role :app, host

set :rails_env, :production

# Where will it be located on a server?
set :deploy_to, "/srv/#{application}"
set :unicorn_conf, "#{deploy_to}/current/config/unicorn.rb"
set :unicorn_pid, "#{deploy_to}/shared/pids/unicorn.pid"

# Unicorn control tasks
namespace :deploy do
  task :restart do
    run "if [ -f #{unicorn_pid} ]; then kill -USR2 `cat #{unicorn_pid}`; else cd #{deploy_to}/current && bundle exec unicorn -c #{unicorn_conf} -E #{rails_env} -D; fi"
  end
  task :start do
    run "cd #{deploy_to}/current && bundle exec unicorn -c #{unicorn_conf} -E #{rails_env} -D"
  end
  task :stop do
    run "if [ -f #{unicorn_pid} ]; then kill -QUIT `cat #{unicorn_pid}`; fi"
  end
end

```

We need a config file for Unicorn. Here is what it may look like:

``` ruby
# define paths and filenames
deploy_to = "/srv/sincapun"
rails_root = "#{deploy_to}/current"
pid_file = "#{deploy_to}/shared/pids/unicorn.pid"
socket_file= "#{deploy_to}/shared/unicorn.sock"
log_file = "#{rails_root}/log/unicorn.log"
err_log = "#{rails_root}/log/unicorn_error.log"
old_pid = pid_file + '.oldbin'

timeout 30
worker_processes 2 # increase or decrease
listen socket_file, :backlog => 1024

pid pid_file
stderr_path err_log
stdout_path log_file

# make forks faster
preload_app true 

# make sure that Bundler finds the Gemfile
before_exec do |server|
  ENV['BUNDLE_GEMFILE'] = File.expand_path('../Gemfile', File.dirname(__FILE__))
end

before_fork do |server, worker|
  defined?(ActiveRecord::Base) and
      ActiveRecord::Base.connection.disconnect!

  # zero downtime deploy magic:
  # if unicorn is already running, ask it to start a new process and quit.
  if File.exists?(old_pid) && server.pid != old_pid
    begin
      Process.kill("QUIT", File.read(old_pid).to_i)
    rescue Errno::ENOENT, Errno::ESRCH
      # someone else did our job for us
    end
  end
end

after_fork do |server, worker|

  # re-establish activerecord connections.
  defined?(ActiveRecord::Base) and
      ActiveRecord::Base.establish_connection
end
```

That should do it. Now you can deploy your app, assuming that you have [RVM](http://beginrescueend.com/) on the server, you can SSH into it and write to /srv directory.

``` bash
cap deploy:setup
cap deploy
```

Deploy should spit a lot of text into the console, and there should be no errors. Verify that our unicorns are launched correctly by logging into the server and running this:

``` bash
$ ps aux | grep sincapun
myuser   24851  2.0  0.1  88480 21024 ?        Sl   11:42   0:00 unicorn master -c /srv/sincapun/current/config/unicorn.rb -E production -D                                         
myuser   24854  0.1  0.1  88480 19732 ?        Sl   11:42   0:00 unicorn worker[0] -c /srv/sincapun/current/config/unicorn.rb -E production -D                                      
myuser   24857  0.1  0.1  88480 19732 ?        Sl   11:42   0:00 unicorn worker[1] -c /srv/sincapun/current/config/unicorn.rb -E production -D                                      
```

To access these unicorns from the internet, you need to put a reverse proxy in front of them. But that is another story.

You can get a full copy of this code [from Github repo](https://github.com/stulentsev/sincapun).



