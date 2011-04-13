---
layout: post
title: Turn jQuery effects off for testing
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

We, at [Crowd Interactive](http://www.crowdint.com), always go with the
TDD + BDD approach when coding new applications.

The usual, we use [RSpec](http://www.rspec.info) for TDD, we use
[Cucumber](http://cukes.info) for BDD, although
lately, I've been using [Steak](https://github.com/cavalle/steak) in place of
Cucumber and so far I am loving it.

When you are using Capybara + Selenium for testing with Cucumber or
Steak, sometimes you can get this error when you write tests for clicks that are
followed by an animation:

{% highlight ruby %}
Selenium::WebDriver::Error::ElementNotDisplayedError:
  Element is not currently visible and so may not be interacted with
{% endhighlight %}

This happens because Capybara is trying to access an element that is
still not visible due to the animation in place, for example, when using
an [overlay window](http://flowplayer.org/tools/demos/overlay/index.html) for a modal form.

To avoid this, you can simply tell jQuery to turn off all effects when
in test mode.

How? Very simple.

All you have to do is add this line within the `<title>`
section of your layout.

### Haml

{% highlight haml %}
= javascript_tag '$.fx.off = true;' if Rails.env.test?
{% endhighlight %}

### ERB

{% highlight erb %}
<%= javascript_tag '$.fx.off = true;' if Rails.env.test? %>
{% endhighlight %}

This way, every time you run your tests you will tell jQuery to disable
effects and your test will pass, and even perform faster.

You can [read the documentation here](http://api.jquery.com/jQuery.fx.off/) for more info on
how to disable jQuery effects off.

Enjoy!
