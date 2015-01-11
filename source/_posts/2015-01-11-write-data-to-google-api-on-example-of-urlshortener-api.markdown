---
layout: post
title: "Write data to Google API on example of UrlShortener API"
date: 2015-01-11 19:19
comments: true
keywords: google-api example ruby
---

I had some free time this weekend and wanted to mess around with Google API. Ultimately I wanted to work with Analytics, but decided to start with something simpler (like UrlShortener) to reduce possible beginner's pain. I was fortunate to decide so, because I spent a couple of hours in debugging anyway.

Google API documentation is weird. It has this short [Getting Started](https://developers.google.com/api-client-library/ruby/start/get_started#build) guide for Ruby, which contains only a few basic steps. [Actual documentation](https://developers.google.com/url-shortener/v1/getting_started?csw=1) is expressed in the universal language of GET/POST requests (to be as language-agnostic as possible). A poor ruby newcomer is basically left to his own devices, there will be little to no help from google. 

Let me share some things I learned. Basic consumption of an API looks like this:

``` ruby
require 'google/api_client'

client = Google::APIClient.new

urlshortener = client.discovered_api('urlshortener')

result = client.execute({
  api_method: urlshortener.url.get,

  parameters: {
    shortUrl: 'http://goo.gl/fbsS',
  }
})
```

Looks simple, right? Yeah. It also doesn't work. You'll get an error when you try to run this.

```
ArgumentError: Missing access token.
	from /Users/sergio/.gem/ruby/2.1.5/gems/signet-0.6.0/lib/signet/oauth_2/client.rb:1009:in `generate_authenticated_request'
	from /Users/sergio/.gem/ruby/2.1.5/gems/google-api-client-0.8.2/lib/google/api_client/request.rb:241:in `to_env'
  ...
```

Documentation forgot to mention that you need to disable authorization in the client.

``` ruby
client = Google::APIClient.new({
  authorization: nil,
})

```

Now, let's say you want to shorten an url. This is `urlshortener.url.insert` method. Should be easy.

``` ruby
client = Google::APIClient.new({
  authorization: nil,
})

urlshortener = client.discovered_api('urlshortener')

result = client.execute({
  api_method: urlshortener.url.insert,

  parameters: {
      longUrl: 'http://google.com',
  }
})

```

But, instead of short url, you get this error in `result.data`

```
{
    "error" => {
         "errors" => [
            {
                      "domain" => "global",
                      "reason" => "required",
                     "message" => "Required",
                "locationType" => "parameter",
                    "location" => "resource.longUrl"
            }
        ],
           "code" => 400,
        "message" => "Required"
    }
}
```

It looks like an error. And it seems to be saying that I somehow did not specify `longUrl` parameter. But I did! Also, what's up with `resource` in `resource.longUrl`. After much googling, some more reading of their gem sources and some debugging on top of that, I was able to figure this out.

You see, `urlshortener.url.insert` is supposed to be a POST request. And it is correctly sent by the client gem. The only problem is with arguments. `parameters` key is what goes into query string for GET requests. But POST requests expect data in request body. Theoretically speaking, the gem could be smart enough to serialize `parameters` as a body if request method is "POST". Sadly, it's not the case. The secret is to use `body_object` key.

``` ruby
result = client.execute({
  api_method: urlshortener.url.insert,

  body_object: {
      longUrl: 'http://google.com',
  }
})
```

`body_object` will be serialized to request body. This will also set a correct "Content-Type" request header ("application/json").

I spent several hours and didn't find a hint of a hint to this in the docs. I know, maintaining good APIs is hard and maintaining good docs is even harder, but isn't google supposed to be example to us all? :)