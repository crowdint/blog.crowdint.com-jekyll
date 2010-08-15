---
layout: post
title: Benchmarking thin vs unicorn
author: Edwin Cruz
email: edwin.cruz@crowdint.com
avatar: f7412b373affd1d6db10752cf5d69315
---

Hi All!, I have been buzzed lately about Unicorn (one of the newest Ruby web application servers) and I've been asked if we have already tested it in production, I answered that no, we're really happy using nginx + haproxy + thin + akamai.

But, I have to admit that after to have a short read about unicorn and having some free time, I started to dig at some already performed benchmarking comparisons between thin and unicorn my friend google showed me some, but all I could find were using really small basic scripts. I needed more realistic numbers, so with some beers in my fridge and no plans with my saturday, I decided to take our 5gb database with ~130 tables, a huge rails project and try to do my own research.

Let's start with my nginx.conf, it looks like this [nginx.conf](/extras/2010/08/15/nginx.conf):

I'm going to use rvm to create isolated environments, since I am familiar with thin, I'll start with it.
{% highlight bash %}
ecruz@ecruz-mbp:ecomm (prod)$ rvm gemset create thin_test

info: Gemset 'thin_test' created.
ecruz@ecruz-mbp:ecomm (prod)$ rvm gemset use thin_test

info: Now using gemset 'thin_test'
ecruz@ecruz-mbp:ecomm (prod)$ gem install --no-rdoc --no-ri bundler
Successfully installed bundler-0.9.26
1 gem installed
ecruz@ecruz-mbp:ecomm (prod)$ bundle install
Fetching source index from http://rubygems.org/
Installing RedCloth (4.2.3) from rubygems repository at http://rubygems.org/ with native extensions 
Installing active_presenter (1.2.1) from rubygems repository at http://rubygems.org/ 
Installing crack (0.1.8) from rubygems repository at http://rubygems.org/ 
Installing httparty (0.6.1) from rubygems repository at http://rubygems.org/ 
Installing bitly (0.5.3) from rubygems repository at http://rubygems.org/ 
Installing cgi_multipart_eof_fix (2.5.0) from rubygems repository at http://rubygems.org/ 
Installing columnize (0.3.1) from rubygems repository at http://rubygems.org/ 
Installing curb (0.7.7.1) from rubygems repository at http://rubygems.org/ with native extensions 
Installing daemons (1.1.0) from rubygems repository at http://rubygems.org/ 
Installing delayed_job (2.0.3) from rubygems repository at http://rubygems.org/ 
Installing fastercsv (1.5.3) from rubygems repository at http://rubygems.org/ 
Installing fastthread (1.0.7) from rubygems repository at http://rubygems.org/ with native extensions 
Installing gem_plugin (0.2.3) from rubygems repository at http://rubygems.org/ 
Installing handsoap (1.1.7) from rubygems repository at http://rubygems.org/ 
Installing json (1.4.6) from rubygems repository at http://rubygems.org/ with native extensions 
Installing linecache (0.43) from rubygems repository at http://rubygems.org/ with native extensions 
Installing mash (0.1.1) from rubygems repository at http://rubygems.org/ 
Installing mime-types (1.16) from rubygems repository at http://rubygems.org/ 
Installing mysql (2.8.1) from rubygems repository at http://rubygems.org/ with native extensions 
Installing nokogiri (1.4.3.1) from rubygems repository at http://rubygems.org/ with native extensions 
Installing oauth (0.4.2) from rubygems repository at http://rubygems.org/ 
Installing packet (0.1.15) from rubygems repository at http://rubygems.org/ 
Installing rmagick (2.13.1) from rubygems repository at http://rubygems.org/ with native extensions 
Installing ruby-debug-base (0.10.3) from rubygems repository at http://rubygems.org/ with native extensions 
Installing ruby-debug (0.10.3) from rubygems repository at http://rubygems.org/ 
Installing twitter (0.6.3) from rubygems repository at http://rubygems.org/ 
Installing twitter_oauth (0.4.3) from rubygems repository at http://rubygems.org/ 
Your bundle is complete! Use `bundle show [gemname]` to see where a bundled gem is installed.

ecruz@ecruz-mbp:ecomm (prod)$ gem install --no-rdoc --no-ri thin
Building native extensions.  This could take a while...
Building native extensions.  This could take a while...
Successfully installed rack-1.2.1
Successfully installed eventmachine-0.12.10
Successfully installed thin-1.2.7
3 gems installed
{% endhighlight %}

Good! I'm happy to have bundler, I might've spent all my afternoon installing required gems by hand. But, we're here to see numbers:
{% highlight bash %}
ecruz@ecruz-mbp:ecomm (prod)$ ruby -v
ruby 1.8.7 (2010-06-23 patchlevel 299) [i686-darwin10.3.1]

ecruz@ecruz-mbp:ecomm (prod)$ thin -e production -p 3000 -d start
ecruz@ecruz-mbp:ecomm (prod)$
{% endhighlight %}

It's ready, my nginx is listening two virtual hosts and I've added to my /etc/hosts these virtual names. Ok, since we're starting, let's use small numbers

{% highlight bash %}
ecruz@ecruz-mbp:blog.crowdint.com (edwin.cruz)$ ab -n 10 -c 10 http://ecomm_thin/store/Apparel

Server Software:        nginx/0.7.64
Server Hostname:        ecomm_thin
Server Port:            80

Document Path:          /store/Apparel
Document Length:        81385 bytes

Concurrency Level:      10
Time taken for tests:   0.713 seconds
Complete requests:      10
Failed requests:        0
Write errors:           0
Total transferred:      817362 bytes
HTML transferred:       813850 bytes
Requests per second:    14.02 [#/sec] (mean)
Time per request:       713.256 [ms] (mean)
Time per request:       71.326 [ms] (mean, across all concurrent requests)
Transfer rate:          1119.10 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       0
Processing:   711  712   0.5    713     713
Waiting:      710  711   0.6    711     712
Total:        711  713   0.4    713     713

Percentage of the requests served within a certain time (ms)
  50%    713
  66%    713
  75%    713
  80%    713
  90%    713
  95%    713
  98%    713
  99%    713
 100%    713 (longest request)
{% endhighlight %}

Mhm, 713 ms per request, I've seen better numbers in our production servers, but well, I'm doing this in my MBP, without memcached and mysql query cached disabled, so, I'd say that: cool!

Now let's work with Unicorn:
{% highlight bash %}

ecruz@ecruz-mbp:ecomm (prod)$ rvm gemset create unicorn_test

info: Gemset 'unicorn_test' created.
ecruz@ecruz-mbp:ecomm (prod)$ rvm gemset use unicorn_test

info: Now using gemset 'unicorn_test'
gecruz@ecruz-mbp:ecomm (prod)$ gem install --no-rdoc --no-ri bundler
Successfully installed bundler-0.9.26
1 gem installed
ecruz@ecruz-mbp:ecomm (prod)$ bundle install
Fetching source index from http://rubygems.org/
   #same gems
   
Your bundle is complete! Use `bundle show [gemname]` to see where a bundled gem is installed.
ecruz@ecruz-mbp:ecomm (prod)$ gem install --no-rdoc --no-ri unicorn
Building native extensions.  This could take a while...
Successfully installed rack-1.2.1
Successfully installed unicorn-1.1.2
2 gems installed
ecruz@ecruz-mbp:ecomm (prod)$ unicorn_rails -p 8000 -E production
{% endhighlight%}

Done... I love bundler, I really do, OK, let's continue with our tests:
{% highlight bash %}
ecruz@ecruz-mbp:ecomm (prod)$ unicorn_rails -p 8000 -E production

ecruz@ecruz-mbp:~ $ ab -n 10 -c 10 http://ecomm_unicorn/store/Apparel

Server Software:        nginx/0.7.64
Server Hostname:        ecomm_unicorn
Server Port:            80

Document Path:          /store/Apparel
Document Length:        81391 bytes

Concurrency Level:      10
Time taken for tests:   0.737 seconds
Complete requests:      10
Failed requests:        0
Write errors:           0
Total transferred:      817581 bytes
HTML transferred:       813910 bytes
Requests per second:    13.56 [#/sec] (mean)
Time per request:       737.212 [ms] (mean)
Time per request:       73.721 [ms] (mean, across all concurrent requests)
Transfer rate:          1083.02 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       0
Processing:    47  332 227.5    406     736
Waiting:       47  332 227.5    406     736
Total:         48  332 227.4    406     737

Percentage of the requests served within a certain time (ms)
  50%    406
  66%    436
  75%    480
  80%    510
  90%    737
  95%    737
  98%    737
  99%    737
 100%    737 (longest request)
ecruz@ecruz-mbp:blog.crowdint.com (edwin.cruz)$ 
{% endhighlight%}

Ok, decent numbers with small difference and knowing that we used a single app server for both, let's simulate a little bit more a production config:

I've adjusted my nginx.conf to support multiples thins
{% highlight bash %}
upstream balancer_thin {
  server 127.0.0.1:3000;
  server 127.0.0.1:3001;
  server 127.0.0.1:3002;
  server 127.0.0.1:3003;
  server 127.0.0.1:3004;
}
{% endhightlight%}

And I started 5 thins listening in ports 3000-3004
{% highlight bash %}
ecruz@ecruz-mbp:ecomm (prod)$ thin -e production -p 3000 -d -s 5 start
Starting server on 0.0.0.0:3000 ... 
Starting server on 0.0.0.0:3001 ... 
Starting server on 0.0.0.0:3002 ... 
Starting server on 0.0.0.0:3003 ... 
Starting server on 0.0.0.0:3004 ... 
ecruz@ecruz-mbp:ecomm (prod)$ 
{% endhightlight%}

I'll use 100 requests with 15 concurrent users(remember that I'm using my MBP).

{% highlight bash %}
ecruz@ecruz-mbp:blog.crowdint.com (edwin.cruz)$ ab -n 100 -c 15 http://ecomm/store/Apparel

Server Software:        nginx/0.7.64
Server Hostname:        ecomm
Server Port:            80

Document Path:          /store/Apparel
Document Length:        81385 bytes

Concurrency Level:      15
Time taken for tests:   2.665 seconds
Complete requests:      100
Failed requests:        0
Write errors:           0
Total transferred:      8173616 bytes
HTML transferred:       8138500 bytes
Requests per second:    37.53 [#/sec] (mean)
Time per request:       399.706 [ms] (mean)
Time per request:       26.647 [ms] (mean, across all concurrent requests)
Transfer rate:          2995.47 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       1
Processing:    54  374 227.0    324     918
Waiting:       51  271 167.3    232     708
Total:         54  375 227.0    325     919

Percentage of the requests served within a certain time (ms)
  50%    325
  66%    494
  75%    528
  80%    594
  90%    656
  95%    858
  98%    919
  99%    919
 100%    919 (longest request)
{% endhightlight%}

Whohoo 399ms, no bad for 15 concurrent users and 100 requests, it's time to see how unicorn performs, I tried to read unicorn documentation and I saw I can adjust unicorn behavior via custom configurator.rb, but since I'm lazy engineer I've modified unicorn gem directly to start with 5 workers:

{% hightlight bash %}
ecruz@ecruz-mbp:ecomm (prod)$ mate /Users/ecruz/.rvm/gems/ruby-1.8.7-p299@unicorn_test/gems/unicorn-1.1.2/lib/unicorn/configurator.rb

ecruz@ecruz-mbp:ecomm (prod)$ unicorn_rails -p 8000 -E production
I, [2010-08-14T20:52:48.684992 #40603]  INFO -- : listening on addr=0.0.0.0:8000 fd=3
I, [2010-08-14T20:52:48.685496 #40603]  INFO -- : worker=0 spawning...
I, [2010-08-14T20:52:48.686348 #40603]  INFO -- : worker=1 spawning...
I, [2010-08-14T20:52:48.687000 #40603]  INFO -- : worker=2 spawning...
I, [2010-08-14T20:52:48.687786 #40603]  INFO -- : worker=3 spawning...
I, [2010-08-14T20:52:48.689268 #40603]  INFO -- : worker=4 spawning...
{% endhightlight%}

Done, I have unicorn running 5 workers and listening at port 8000, let's send it the same traffic:
{% hightlight bash %}
ecruz@ecruz-mbp:blog.crowdint.com (edwin.cruz)$ ab -n 100 -c 15 http://ecomm_unicorn/store/Apparel

Server Software:        nginx/0.7.64
Server Hostname:        ecomm_unicorn
Server Port:            80

Document Path:          /store/Apparel
Document Length:        81391 bytes

Concurrency Level:      15
Time taken for tests:   2.672 seconds
Complete requests:      100
Failed requests:        0
Write errors:           0
Total transferred:      8175813 bytes
HTML transferred:       8139100 bytes
Requests per second:    37.42 [#/sec] (mean)
Time per request:       400.860 [ms] (mean)
Time per request:       26.724 [ms] (mean, across all concurrent requests)
Transfer rate:          2987.65 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       1
Processing:    69  368 165.3    334     642
Waiting:       69  368 165.3    334     641
Total:         69  368 165.3    334     642

Percentage of the requests served within a certain time (ms)
  50%    334
  66%    504
  75%    523
  80%    543
  90%    587
  95%    614
  98%    622
  99%    642
 100%    642 (longest request)
ecruz@ecruz-mbp:blog.crowdint.com (edwin.cruz)$ 
{% endhighlight %}

Look at that!!! the difference is minimal, this is a really huge surprise.

Ok, I'll run tests using extreme over loading against the same 5 thins and unicorn with 5 workers (For each test I restarted my machine):
{% highlight bash %}
ecruz@ecruz-mbp:blog.crowdint.com (edwin.cruz)$ ab -n 1000 -c 100 http://ecomm_thin/store/Apparel

Server Software:        nginx/0.7.64
Server Hostname:        ecomm_thin
Server Port:            80

Document Path:          /store/Apparel
Document Length:        81385 bytes

Concurrency Level:      100
Time taken for tests:   31.030 seconds
Complete requests:      1000
Failed requests:        4
   (Connect: 0, Receive: 0, Length: 4, Exceptions: 0)
Write errors:           0
Total transferred:      81736483 bytes
HTML transferred:       81385312 bytes
Requests per second:    32.23 [#/sec] (mean)
Time per request:       3102.981 [ms] (mean)
Time per request:       31.030 [ms] (mean, across all concurrent requests)
Transfer rate:          2572.39 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    1   1.0      0      10
Processing:    55 3035 1467.6   2880    7831
Waiting:       54 2433 1276.4   2202    7369
Total:         55 3036 1468.1   2880    7833

Percentage of the requests served within a certain time (ms)
  50%   2880
  66%   3314
  75%   3584
  80%   3794
  90%   4485
  95%   6825
  98%   7073
  99%   7369
 100%   7833 (longest request)
ecruz@ecruz-mbp:blog.crowdint.com (edwin.cruz)$ 
{% endhighlight %}
Wow! my machine survived...

{% highlight bash%}
ecruz@ecruz-mbp:blog.crowdint.com (edwin.cruz)$ ab -n 1000 -c 100 http://ecomm_unicorn/store/Apparel

Server Software:        nginx/0.7.64
Server Hostname:        ecomm_unicorn
Server Port:            80

Document Path:          /store/Apparel
Document Length:        81391 bytes

Concurrency Level:      100
Time taken for tests:   27.692 seconds
Complete requests:      1000
Failed requests:        0
Write errors:           0
Total transferred:      81758141 bytes
HTML transferred:       81391000 bytes
Requests per second:    36.11 [#/sec] (mean)
Time per request:       2769.227 [ms] (mean)
Time per request:       27.692 [ms] (mean, across all concurrent requests)
Transfer rate:          2883.18 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.8      0       4
Processing:    81 2639 441.8   2671    3178
Waiting:       81 2638 441.8   2670    3172
Total:         86 2639 441.1   2671    3179

Percentage of the requests served within a certain time (ms)
  50%   2671
  66%   2759
  75%   2868
  80%   2923
  90%   2976
  95%   3008
  98%   3048
  99%   3099
 100%   3179 (longest request)
ecruz@ecruz-mbp:blog.crowdint.com (edwin.cruz)$
{% endhighlight %}

Ok, there're the numbers, think about if it's worth to change any current infrastructure, monitoring tools, deployment scripts, etc to switch between each other. We've taken ours x).

Machine used:
Processor: 2.4 GHz Intel Core i5
Memory: 4GB 1067 MHz DDR3
Mac OS X: 10.6.3
