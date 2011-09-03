---
layout: post
title: Ruby benchmarks
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

I recently started a new client project. Lucky me, [Rails 3.1 has just
been released](http://weblog.rubyonrails.org/2011/8/31/rails-3-1-0-has-been-released)
so, it means I'll get to learn a bunch of things while
building it, you know, the fancy [asset pipeline](http://edgeguides.rubyonrails.org/asset_pipeline.html)
stuff everyone's talking about and so on.

I wrote a couple of rspec specs and cucumber features, made them pass, and
while I was about to start now features, decided to stop to check the
current state of all the different rubies to try them and pick the
faster one, at least for development.

Well, I was already using *MRI 1.9.2*, so, for research purposes
I proceeded to install *MRI 1.9.3-preview*, *Rubinus 1.2.5dev*, *REE-1.8.7* and
*jRuby 1.6.3*. (Thank you [RVM](http://beginrescueend.com/) for making this as
easy as possible).

Now, so far, my app has 4 rspec specs and 3 cucumber scenarios, but
even so, I came up with interesting time results.

Below the results of running *time rake* for each
implementation.tnueotnuo

## MRI 1.9.2

{% highlight bash %}

# Rspec
Finished in 0.13588 seconds
4 examples, 0 failures

# Cucumber
3 scenarios (3 passed)
10 steps (10 passed)
0m0.756s

# Time
real	0m24.259s
user	0m20.752s
sys	0m2.530s

{% endhighlight %}

## MRI 1.9.3-preview

{% highlight bash %}
# Rspec
Finished in 0.05074 seconds
4 examples, 0 failures

# Cucumber
3 scenarios (3 passed)
10 steps (10 passed)
0m0.379s

# Time
real	0m13.354s
user	0m10.120s
sys	0m1.546s

{% endhighlight %}

## jRuby

{% highlight bash %}

# RSpec
Finished in 0.186 seconds
4 examples, 0 failures

# Cucumber
3 scenarios (3 passed)
10 steps (10 passed)
0m2.141s

# Time
real	1m3.823s
user	2m9.875s
sys	0m7.005s

{% endhighlight %}

## Rubinus

{% highlight bash %}

# RSpec
Finished in 0.27061 seconds
4 examples, 0 failures

# Cucumber
3 scenarios (3 passed)
10 steps (10 passed)
0m2.011s

# Time
real	0m47.328s
user	1m1.049s
sys	0m2.938s

{% endhighlight %}

## REE

Note that I used the following environment variables for this one:

{% highlight bash %}
RUBY_GC_MALLOC_LIMIT="50000000"
RUBY_HEAP_MIN_SLOTS="500000"
RUBY_HEAP_SLOTS_GROWTH_FACTOR="1"
RUBY_HEAP_SLOTS_INCREMENT="250000"
{% endhighlight %}

To understand how this affects its performance, check out [this post](http://blog.crowdint.com/2010/12/07/improving-your-dev-life-with-ree.html)

{% highlight bash %}
# RSpec
Finished in 0.05163 seconds
4 examples, 0 failures

# Cucumber
3 scenarios (3 passed)
10 steps (10 passed)
0m0.277s

# Time
real	0m10.769s
user	0m7.519s
sys	0m2.106s
{% endhighlight %}

## Conclusion

It looks like there's huge speed improvements from 1.9.2 to 1.9.3. It took
24 seconds on 1.9.2 and 12 on 1.9.3, that's a lot of speed boost!

JRuby, at 1 minute where the other rubies took seconds is light years
behind them, though, I might be doing something wrong. I really have no practical
experience with it and I just benchmarked it out of the box.

Rubinus felt kind of slow too, but, I consider it to be
work in progress, or at least haven't heard of production apps running
on it yet, which doesn't necesarily mean there aren't any. I'd actually
love to use it on production for a few weeks when we launch this app
just to see how it behaves in real life.

And last but not least, a properly configured REE kicks butt. No surprise there, we've been using it
on another app on production for a year now with good performance and no
problems at all so far.

So, there it is. I realize it is a small benchmark, but perhaps I can
revisit it when we have more specs and more features to see if that
changes how the rubies behave.

Cheers!
