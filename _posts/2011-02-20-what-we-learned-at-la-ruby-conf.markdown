---
layout: post
title: What we learned at LA Ruby Conf
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

Earlier this month I decided to go to [LA Ruby Conf 2011](http://www.larubyconf.com).
I was already in San Francisco, and, since I was just an hour away via
plane, I jumped into one and attended the conference sessions on Saturday.

As expected, the organization and the conferences were awesome. Kudos to
the organizators.

Now, a small recap of what I learned there.

## ActiveSupport Three - It is finally getting interesting

### Bryan Lyles

[Bryan Liles](http://smartic.us/), being the Greatest
Man Alive gave an awesome talk on ActiveSupport and its similarities
with [Ruby Facets](http://rubyworks.github.com/facets/).

To be honest, I have never used Facets, but it seems it's worth taking a
look.

Sadly, Bryan was time constrained and he just gave a small part of what
seems to be a big, interesting talk about those two libraries.

The most interesting part of this talk for me was to learn that Rails 3
ships with a lot of built-in instrumentation, so, expect a lot of gems
to measure performance of the applications more acurately in the near
future.

## NinjaScript: JavaScript so unobtrusive, you won't see it coming.

### Evan Dorn

These guys are making an awesome effort of separating the Javascript
dependency out of the HTML and the CSS.

So, they wrote [NinjaScript](https://github.com/LRDesign/NinjaScript).
From their github page:

NinjaScript is a jQuery library designed to allow unobstrusive scripting simply and easily.
Essentially, we use jQuery selectors to apply behavior the same way we use CSS stylesheets
to apply stylings.
Additionally, NinjaScript makes it easy to package up useful behaviors and apply
them quickly to disparate elements, or in different projects.

Looks promising, but there's still a lot of Javascript involved, and I
usually try to stay away from Javascript as much as I can, I am old
school.

Lucky for me, they also wrote the [Mizugumo](https://github.com/LRDesign/mizugumo)
gem.

Mizugumo is a gem designed to provide Rails with JavaScript and AJAX behavior that is:

* Absolutely seamless
* Completely unombtrusive - no markup whatsoever in your HTML and
* Gracefully-degrading: defaults to fully functional and sensible page-reload behavior when JavaScript is not available.

Now, that's more like it.

You can see it in action [right here](http://mizugumo-demo.lrdesign.com/products).
Try using that UI both with your JS enabled and disabled. See what
happens.

You're supposed to get all that sweetness just by dropping the gem and 
doing some minimal configuration.

Haven't tried it myself, but probably will soon.

## The Rails Tutorial Story

### Michael Hartl

I have never heard about [The Rails Tutorial](http://ruby.railstutorial.org/).

Michael Hartl told us the story behind it, and how he ended up writing
all of that, and let me tell you something: it was *Inspiring*.

Talk about perseverance. I won't ruin this story for you, because,
obviously I can't tell it as good nor accurate as he did.

All I can tell you is, take a look at the site, do yourself some good,
buy the screencast / PDF bundle.

It does look like a very good way to learn Rails 3.

## How To Jam In Code

### Ron Evans

You probably know Ron Evans, he knows everyone. I had already seen this
talk when he gave it at [MagmaRails](http://www.magmarails.com).

Awesome. Period. Ron knows how to involve the crowd on his talks.

If you haven't seen this talk, you are out of luck since now he's more
into [Kids Ruby](http://www.kidsruby.com/). Can't wait to see that
talk later in the year, Bravo Ron! for thinking of the kids. Someone has
to.

## Working in Virtual Machines, the Vagrant Way

### Mitchell Hashimoto

[Vagrant](http://vagrantup.com/) is a tool for building and distributing
virtualized development environments.

I wonder if you can do some funky stuff with vagrant where you create
Windows virtual machines and run your cucumber features or Selenium
tests automagically on them.

I will have to play with this eventually.

## Securing Your Rails App

### Jim Weirich, Matt Yoho

Basics on Rails app security. I was sort of expecting something more
advanced from Jim Weirich, but, it was good.

Learned about the existence of these two papers:

* [Ruby on Rails Security Guide](http://guides.rubyonrails.org/security.html)
* [OWASP Ruby on Rails Security Guide](https://www.owasp.org/images/8/89/Rails_Security_2.pdf)

No Rails developer should be allowed to send code to production without
reading those.

Also learned that security holes can slip everywhere, [even to the best
people out there](http://martinfowler.com/snips/201102031214.html).

No one is safe on the interwebs.

By the way, if you've finally decided to be more serious about security, take
a look at [Metasploit](http://www.metasploit.com/framework/). A
framework to perform penetration testing on your Network.

## Twitter Mobile

### Ben Sandofsky

This talk was full of interesting stuff and could easily be it's own
post by itself.

Ben described how they work at twitter. I'll highlight what I think was the most
interesting parts of the talk.

* The record of tweets per second was set last New Years eve at 6,939
  tps.
* Twitter is heavily Service oriented. They didn't started that way, but
  they have been transitioning in the last few years. Doesn't sound like
  an easy task.
* They created a DSL that allows them to create new Service endpoints
  with just a few lines of code. Clever.
* They use YAJL for JSON. Twice the performance just by dropping a gem.
* They use GD2 for image processing since it's way faster than Image
  Magick.
* They use [Typhoeus](https://github.com/dbalatero/typhoeus) for
  parallel HTTP requests betwen their services.
* They work in small agile teams
* Usually composed of 1 PM, 1 Designer, 3 Devs
* Iterative development
* They believe in continuous shipping. They usually do 3 deploys a
  day. The record? 7 in one day.
* They have a rigorous test suite in place. It runs for 1 hour by
  itself but they built an awesome fleet of servers that can run it in
  *2 minutes*.
* Sadly, no details on how they do that :(
* They do beleive in Pair programming, but it's up to the teams to do
  actually do it.
* They do require code review before pushing something.
* They deliver the features to small amounts of users before full
  release.
* They have no QA team. Every team is responsible of their features and
  their code. They believe that developers tend to become lazy and
  careless when there's a QA team in place.
* Company culture is key to success.

## Conclusion

I missed a review on some of the conferences, but I just included on
this post what I thougt was the most interesting part of the event.

LARubyconf just made me reassure my thoughts about small regional
conferences being way better than the big ones.

Many thanks to Ron Evans of [The Hybrid Group](http://hybridgroup.com/)
for inviting us to have delicious brunch the next day at his place. Good
times.

Looking forward to my next conference. Don't know where or when yet,
[Ruby Nation](http://www.rubynation.org//)'s lineup looks very promising.

Until the next time.
