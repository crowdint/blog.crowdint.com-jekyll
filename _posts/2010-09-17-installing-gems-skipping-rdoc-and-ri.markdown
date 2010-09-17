---
layout: post
title: Installing gems skipping RDoc and RI
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
published: true
---

Because, let's be honest, these days, we're always connected to the internet.

Personally, I've never used the RDoc included on gem installations.

If I need info about a class, method, module, I ask uncle *Google*.

Besides, it gets kind of annoying, specially when there's gems that take longer to generate the RDoc, than it takes for the actual gem to install.

So, here's a tip for those who don't want to wait for gem to install RDoc you'll never use.

It is as simple as, opening or creating a *.gemrc* file in your home directory, and adding the following line:

{% highlight bash %}
gem: --no-rdoc --no-ri
{% endhighlight %}

And that's it. You're setting those options as default when using the gem command. So, the next time you install a gem, it will skip the part where it compiles the gem's documentation.

There you go, enjoy!

