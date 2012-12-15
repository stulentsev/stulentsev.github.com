---
layout: post
title: "Using Sidekiq with redis_failover gem"
date: 2012-12-15 18:23
comments: true
categories: ruby redis
keywords: ruby sidekiq resque redis_failover redis worker async background processing job task
---

[Sidekiq](http://mperham.github.com/sidekiq/) works pretty well with standard redis client. This is an example of configuring it ([from official wiki](https://github.com/mperham/sidekiq/wiki/Advanced-Options)):

``` ruby
Sidekiq.configure_server do |config|
  config.redis = { :url => 'redis://redis.example.com:7372/12', :namespace => 'mynamespace' }
end

# When in Unicorn, this block needs to go in unicorn's `after_fork` callback:
Sidekiq.configure_client do |config|
  config.redis = { :url => 'redis://redis.example.com:7372/12', :namespace => 'mynamespace' }
end
```

But, what if you need a special redis client that can't be initialized with redis URL? No worries. Setter for `Sidekiq#redis` also accepts a [ConnectionPool](https://github.com/mperham/connection_pool). Here we have full control over how we initialize our redis client. Here's how you may initialize Sidekiq with a bunch of RedisFailover clients:

``` ruby
Sidekiq.configure_server do |config|
  pool = ConnectionPool.new(:size => 10, :timeout => 3) do
    zkservers = 'localhost:2181,localhost:2182,localhost:2183'
    RedisFailover::Client.new namespace: 'my_service', zkservers: zkservers
  end
  
  config.redis = pool
end

# do the same for in configure_client
```

You can even read Sidekiq's `concurrency` setting and pass it to the connection pool initializer. Now your sidekiq farm doesn't care about redis crashes :)

Happy sidekiqing :)