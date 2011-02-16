---
layout: post
title: What we learned at LA Ruby Conf
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

Earlier this month I decided to go to [LA Ruby Conf 2011](http://www.larubyconf.com).
I was already in San Francisco, and, since I think small regional conferences are far better
than the "big" ones, because of being single tracked and have usually
have better content, I jumped into a plane and attended the conferences on Saturday.

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

You can see it in action [right here](http://mizugumo-demo.lrdesign.com/products).
Try using that UI both with your JS enabled and disabled. See what
happens.

All that is supposed to happen just by dropping the gem and minimal
configuration.

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

Awesome. Period.

If you haven't seen this talk, you are out of luck since now he's more
into [Kids Ruby](http://www.kidsruby.com/). Can't wait to see that
talk later in the year, Bravo Ron! for thinking of the kids. Someone has
to.




