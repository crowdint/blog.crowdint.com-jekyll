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
Steak, sometimes you can get an error when you write tests for clicks that are
followed by an animation.

For example:

{% highlight ruby %}

scenario "New resource" do
  click_link 'New Resource'
  # Shows up an overlay window with the form that
  # takes 5 seconds to show up

  fill_in 'Name', :with => 'Some value'
end
{% endhighlight %}

You get the following error:

{% highlight ruby %}
Selenium::WebDriver::Error::ElementNotDisplayedError:
  Element is not currently visible and so may not be interacted with
{% endhighlight %}

This happens because Capybara is trying to access the input element, but
since the animation is still happening it is not visible yet.

To avoid this, you can simply tell jQuery to turn off all effects when
in test mode.

How? Very simple.

All you have to do is add this line within the `<head>`
section of your layout.

### Haml

{% highlight haml %}
= javascript_tag '$.fx.off = true;' if Rails.env.test?
{% endhighlight %}

### ERB

{% highlight erb %}
<%= javascript_tag '$.fx.off = true;' if Rails.env.test? %>
{% endhighlight %}

This way, every time you run your tests you will be telling jQuery to disable
all effects and your test will pass, and, in some cases, even perform faster.

You can [read the documentation here](http://api.jquery.com/jQuery.fx.off/) for more info on
how to disable jQuery effects off.

Enjoy!
