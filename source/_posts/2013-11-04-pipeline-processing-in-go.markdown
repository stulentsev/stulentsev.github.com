---
layout: post
title: "Pipeline processing in Go"
date: 2013-11-04 12:13
comments: true
categories: golang
keywords: go golang pipeline goroutine concurrency
---

Pipeline processing is a very powerful design idiom. You have some simple building blocks that you can arrange in different combinations to perform complex tasks. A classic example of that is unix command line. Each tool is very simple. It does only job. But it still amazes me regularly, what you can achieve by simply combining those tools into a pipeline and feeding/piping data through it.

Say, you're building an RSS reader that shows new posts live. Implementing it in a regular procedural manner is easy. Something like this (pseudo-code):

<pre>
  loop {
    fetch posts
    for each post {
      if we have not yet seen this post {
        mark post seen
        show it to user
      }
    }
  }
</pre>

Say, now we want to do a focused reading. Which means that we only want to see a subset of posts which satisfy some arbitrary criteria (filter by tags, etc.). No problemo, we just add one conditional in there.

<pre>
  loop {
    fetch posts
    for each post {
      if post is interesting {
        if we have not yet seen this post {
          mark post seen
          show it to user
        }
      }
    }
  }
</pre>

Now it's getting a little bit difficult to read and it will get worse. All business rules are in the same pile of code and it may be difficult to tell them apart. 

But if you think about it, there is a pipeline that consists of several primitive segments:

{% img center /images/uploads/2013/11/04/simple_pipeline.png %}

Each individual block is completely separate from each other (well, maybe except that "select unseen" and "mark all seen" might use the same storage). If we were to, say, remove caching (and stop serving cached content), we'd only have to take out those two segments. If we want to change how content is presented to user (print to terminal, send to text-to-speect engine, ...), we only have to replace the final segment. The rest of the pipeline stays untouched. And the aforementioned tag filtering - we just insert it after fetcher:

{% img center /images/uploads/2013/11/04/pipeline_with_filter.png %}

In the pipeline, each segment can either swallow a message or pass it on (after applying some transformation (optional)). Two simple rules, infinite flexibility.

Go language allows for natural expression of this design: goroutines as segments, connected by channels.

Here's an intentionally primitive example, which filters and transforms a stream of integers.

``` go
package main

import (
  "fmt"
  "time"
)

// type alias for our pipeline segment
// segment is a function that reads from a stream of integers
//  and writes to a stream of integers
type pipelineSegment func(in, out chan int)

func main() {
  // construct our pipeline. Put generator first, then filters/modifiers
  out := makePipeline(generator, onlyOdd, plusOne, squared, plusOne)

  for v := range out {
    fmt.Printf("Resulting value: %d\n", v)
  }
}

// This simply generates sequential integers in infinite loop.
func generator(_, out chan int) {
  i := 0

  for {
    out <- i
    i += 1
    time.Sleep(100 * time.Millisecond)
  }
}

// Filter. Selects only odd integers. Even integers are swallowed.
func onlyOdd(in, out chan int) {
  defer close(out)
  for val := range in {
    if val%2 == 1 {
      out <- val
    }
  }
}

// Modifier. Adds 1 and passes on.
func plusOne(in, out chan int) {
  defer close(out)
  for val := range in {
    out <- val + 1
  }
}

// Modifier. Passes a square of incoming integer.
func squared(in, out chan int) {
  defer close(out)
  for val := range in {
    out <- val * val
  }
}

// Generates pipeline out of individual segments. 
// Returns an "exhaust pipe", from which fully processed integers can be read.
func makePipeline(segments ...pipelineSegment) (out chan int) {
  current_input := make(chan int)
  var current_output chan int

  for _, seg := range segments {
    current_output = make(chan int)
    go seg(current_input, current_output)

    current_input = current_output
  }

  return current_output
}
```

Produced output:

<pre>
  Resulting value: 5
  Resulting value: 17
  Resulting value: 37
  Resulting value: 65
  Resulting value: 101
  Resulting value: 145
  Resulting value: 197
  ...
</pre>

First generated integer is 0. It's even (well, certainly not odd), so it does not make it past filter. 

Next one is 1. It passes filter. Then it gets +1, so it's now 2. Then it's squared and becomes 4. And finally, one more +1 which results in 5. There are no more segments, so this value is read from the output pipe and printed to terminal.

Hope this was useful.