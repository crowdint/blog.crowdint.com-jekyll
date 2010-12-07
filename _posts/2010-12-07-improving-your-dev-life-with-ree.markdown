---
layout: post
title: Improving your development life with Ruby Enterprise Edition
author: Edwin Cruz
email: edwin.cruz@crowdint.com
avatar: f7412b373affd1d6db10752cf5d69315
published: true
---

Have you ever wondered if you can do something to make your ruby development even faster?
Are you tired of waiting for rspec to finish?
Is your rails application eating more and more memory and you have to restart your app once in a while?

Ok, This is one of those paths to improve your ruby dev life, nothing new, but it's very effective.

## What is REE?

As you can read on the ruby site: "Ruby is a dynamic, open source programming language with a focus on simplicity and productivity. It has an elegant syntax that is natural to read and easy to write.... and it continues". But the most important part, as of this post, is its garbage collection technique: "[mark-and-sweep](http://en.wikipedia.org/wiki/Garbage_collection_%28computer_science%29#Na.C3.AFve_mark-and-sweep)".

Modern requirements has pushed developers to implement and improve current techniques to recycle memory and maximize resources.

Ruby Enterprise Edition has come to attack this matter, replacing the garbage collector algorithm with "[copy-on-write](http://en.wikipedia.org/wiki/Copy-on-write)", implementing a forking model(reducing application's memory usage by 33% on average) and the most important part (again, as of this post): the ability to [tweak the garbage collector](http://www.rubyenterpriseedition.com/documentation.html#_garbage_collector_performance_tuning) via environment variables.

Based on the documentation and some research made by well known companies (twitter, 37signals), you can gain a lot of performance if you tweak the garbage collector settings used by ruby enterprise edition, I'll show you my own benchmark tests. Being part of a release engineering team, I need to run all tests as fast as possible, finding this technique highly effective.

## Installing REE

I'll guide you through this post assuming that you already have [rvm](http://rvm.beginrescueend.com/rvm/install/) installed on your computer.

{% highlight bash %}
ecruz@ecruz-mbp:project (master)$ rvm install ree
#go and grab a drink, preferably coffee or beer
#some output
#done.
ecruz@ecruz-mbp:project (master)$ rvm use ree
ecruz@ecruz-mbp:project (master)$ rvm list

rvm rubies

=> ree-1.8.7-2010.02 [ x86_64 ]
   ruby-1.8.7-p302 [ x86_64 ]
   ruby-1.9.1-p378 [ x86_64 ]
ecruz@ecruz-mbp:project (master)$ ruby -v
ruby 1.8.7 (2010-04-19 patchlevel 253) [i686-darwin10.4.0], MBARI 0x6770, Ruby Enterprise Edition 2010.02

ecruz@ecruz-mbp:project (master)$
{% endhighlight %}

Done, you are ready to rock using ruby enterprise edition, next step, install gems under ree

{% highlight bash %}
ecruz@ecruz-mbp:project (master)$ rvm gemset create my_new_gems
ecruz@ecruz-mbp:project (master)$ bundle install
Installing ...
Installing ....
Using bundler (1.0.3) 
Your bundle is complete! It was installed into /Users/ecruz/.rvm/gems/ree-1.8.7-2010.02@my_new_gems

ecruz@ecruz-mbp:project (master)$
{% endhighlight %}

Hint: go to this [post](http://blog.crowdint.com/2010/08/17/use-a-project-specific-ruby-version-rvm.html) and make rvm always use your desired gemset@ruby

Ok, perfect... let's play with our new toy, I'll run our rspec suite to demonstrate the ree power
{% highlight bash %}
ecruz@ecruz-mbp:project (develop)$ time rake spec
=> Building fixtures
..
... in 62.33s

SLOW SPEC: 5.9991  Test A
SLOW SPEC: 5.3510  Test B
SLOW SPEC: 7.8485  Test C
SLOW SPEC: 7.6710  Test D
SLOW SPEC: 5.8465  Test E
SLOW SPEC: 5.9972  Test F
SLOW SPEC: 5.9707  Test G
SLOW SPEC: 5.4557  Test H
SLOW SPEC: 6.0631  Test I
SLOW SPEC: 5.4861  Test J
1763/1763:     100% |##########################################| Time: 00:10:43

Finished in 643.211296 seconds

1763 examples, 0 failure, 1 pending

real  13m45.186s
user  11m0.745s
sys   0m20.082s
{% endhighlight %}

Mmhm, that was... 'fast', I wonder how previous numbers performed.

{% highlight bash %}
ecruz@ecruz-mbp:project (develop)$ rvm use 1.8.7
Using /Users/ecruz/.rvm/gems/ruby-1.8.7-p302
ecruz@ecruz-mbp:project (develop)$ rvm gemset create my_new_gems
ecruz@ecruz-mbp:project (develop)$ rvm gemset use my_new_gems
Now using gemset 'my_new_gems'
ecruz@ecruz-mbp:project (develop)$ bundle install
Installing ...
Installing ....
Using bundler (1.0.3) 
Your bundle is complete! It was installed into /Users/ecruz/.rvm/gems/ruby-1.8.7-p302@my_new_gems

ecruz@ecruz-mbp:project (develop)$ time rake spec
=> Building fixtures
...
in 84.92s

SLOW SPEC: 8.6082  Test A
SLOW SPEC: 10.3736 Test B
SLOW SPEC: 11.3856 Test C
SLOW SPEC: 7.4864  Test D
SLOW SPEC: 6.5998  Test E
SLOW SPEC: 8.8307  Test F
SLOW SPEC: 8.8013  Test G
SLOW SPEC: 8.8126  Test H
SLOW SPEC: 8.9622  Test I
SLOW SPEC: 8.9033  Test J
1763/1763:     100% |##########################################| Time: 00:15:30

Finished in 930.143889 seconds

1763 examples, 0 failure, 1 pending

real  19m40.629s
user  16m13.691s
sys   0m21.632s
{% endhighlight %}

Wow! I hadn't noticed the huge difference between ruby 1.8.7 and ruby enterprise edition, that was ~1.4x faster!

Ok, I got huge difference between two versions, the question is, can it be improved? The answer is found in REE documentation, section [Garbage Collector Performance Tunning](http://www.rubyenterpriseedition.com/documentation.html#_garbage_collector_performance_tuning). Twitter has published their production settings, let's see what we can get.

{% highlight bash %}
ecruz@ecruz-mbp:project (develop)$ rvm use ree
Using /Users/ecruz/.rvm/gems/ree-1.8.7-2010.02
ecruz@ecruz-mbp:project (develop)$ rvm gemset use my_new_gems
Now using gemset 'my_new_gems'
ecruz@ecruz-mbp:project (develop)$ time RUBY_HEAP_MIN_SLOTS=500000 RUBY_HEAP_SLOTS_INCREMENT=250000 RUBY_HEAP_SLOTS_GROWTH_FACTOR=1 RUBY_GC_MALLOC_LIMIT=50000000 rake spec
=> Building fixtures
..
...in 44.06s
SLOW SPEC: 6.2397  Test C
SLOW SPEC: 8.4131  Test D
SLOW SPEC: 5.6498  Test E
1763/1763:     100% |##########################################| Time: 00:06:53

Finished in 413.352603 seconds

1763 examples, 0 failure, 1 pending

real  9m26.457s
user  6m46.696s
sys   0m20.475s
{% endhighlight %}

You should've seen my face when I came back and saw the results, that meant 2.2x faster!

My next thought was: I have to make this a permanent change in my system, so it was easy:
{% highlight bash %}
ecruz@ecruz-mbp:project (develop)$ echo "RUBY_HEAP_MIN_SLOTS=500000" >> ~/.bash_profile
ecruz@ecruz-mbp:project (develop)$ echo "RUBY_HEAP_SLOTS_INCREMENT=250000" >> ~/.bash_profile
ecruz@ecruz-mbp:project (develop)$ echo "RUBY_HEAP_SLOTS_GROWTH_FACTOR=1" >> ~/.bash_profile
ecruz@ecruz-mbp:project (develop)$ echo "RUBY_GC_MALLOC_LIMIT=50000000" >> ~/.bash_profile

{% endhighlight %}

After I put new configs to always apply, I decided to benchmark the app, via web, and I found this:

### Using Standard Ruby 1.8.7
{% highlight bash %}
ecruz@ecruz-mbp:project (develop)$ ab -n 1000 -c 100 http://127.0.0.1:8080/store/Category/1/1/100/new
This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)

Server Software:        
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /store/Category/1/1/100/new
Document Length:        97015 bytes

Concurrency Level:      100
Time taken for tests:   56.476 seconds
Complete requests:      1000
Failed requests:        0
Write errors:           0
Total transferred:      97360264 bytes
HTML transferred:       97015000 bytes
Requests per second:    17.71 [#/sec] (mean)
Time per request:       5647.631 [ms] (mean)
Time per request:       56.476 [ms] (mean, across all concurrent requests)
Transfer rate:          1683.51 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.8      0       6
Processing:    89 5378 899.5   5653    6163
Waiting:       88 5377 899.5   5652    6162
Total:         92 5378 898.9   5653    6165
{% endhighlight %}

### Ruby Enterprise Edition
{% highlight bash %}
ecruz@ecruz-mbp:project (develop)$ ab -n 1000 -c 100 http://127.0.0.1:8080/store/Category/1/1/100/new
This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)


Server Software:        
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /store/Category/1/1/100/new
Document Length:        97015 bytes

Concurrency Level:      100
Time taken for tests:   45.212 seconds
Complete requests:      1000
Failed requests:        0
Write errors:           0
Total transferred:      97360134 bytes
HTML transferred:       97015000 bytes
Requests per second:    22.12 [#/sec] (mean)
Time per request:       4521.241 [ms] (mean)
Time per request:       45.212 [ms] (mean, across all concurrent requests)
Transfer rate:          2102.92 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   1.0      0       5
Processing:    83 4285 706.4   4456    5027
Waiting:       82 4284 706.4   4455    5026
Total:         88 4286 705.5   4456    5028
{% endhighlight %}

### Ruby Enterprise Edition with twitter settings
{% highlight bash %}
ecruz@ecruz-mbp:project (develop)$ ab -n 1000 -c 100 http://127.0.0.1:8080/store/Category/1/1/100/new
This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)


Server Software:        
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /store/Category/1/1/100/new
Document Length:        97015 bytes

Concurrency Level:      100
Time taken for tests:   31.348 seconds
Complete requests:      1000
Failed requests:        0
Write errors:           0
Total transferred:      97360048 bytes
HTML transferred:       97015000 bytes
Requests per second:    31.90 [#/sec] (mean)
Time per request:       3134.784 [ms] (mean)
Time per request:       31.348 [ms] (mean, across all concurrent requests)
Transfer rate:          3033.01 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.9      0       5
Processing:   210 2962 562.4   3074    3873
Waiting:      210 2961 562.4   3073    3872
Total:        215 2963 561.6   3074    3873
{% endhighlight %}

### Ruby Enterprise Edition with 37signals settings
37signals has published their production settings too, and I tried to apply them in my project and I got:
{% highlight bash %}
ecruz@ecruz-mbp:project (develop)$ ab -n 1000 -c 100 http://127.0.0.1:8080/store/Category/1/1/100/new
This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)


Server Software:        
Server Hostname:        127.0.0.1
Server Port:            8080

Document Path:          /store/Category/1/1/100/new
Document Length:        97015 bytes

Concurrency Level:      100
Time taken for tests:   46.230 seconds
Complete requests:      1000
Failed requests:        0
Write errors:           0
Total transferred:      97360135 bytes
HTML transferred:       97015000 bytes
Requests per second:    21.63 [#/sec] (mean)
Time per request:       4622.981 [ms] (mean)
Time per request:       46.230 [ms] (mean, across all concurrent requests)
Transfer rate:          2056.64 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.9      0       5
Processing:    79 4390 760.8   4473    5830
Waiting:       78 4390 760.8   4472    5829
Total:         84 4391 760.0   4473    5830
{% endhighlight %}

For all my tests, I used unicorn with 3 workers running in production mode and memcached enabled. Another huge difference is memory usage, I've seen flat, much less memory utilization and response time has been responding ~1.8x faster. You should definitively consider using this approach in your production environments.

*Disclaimer, this is not an extensive research about REE and it's features, it's just intended to show small tweaks that can make a huge difference in your daily development work.
