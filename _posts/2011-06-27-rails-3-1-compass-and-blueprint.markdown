---
layout: post
title: Rails 3.1, Compass and Blueprint
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
---

So, we all know that Rails 3.1 comes with a Sass compiler. But still,
there are a few of us that used [compass](http://compass-style.org/) on
most of our pre 3.1 projects not just
because of the sass, but because of the [blueprint](http://www.blueprintcss.org/)
integration.

Well, it's very easy to use Compass with Rails 3.1 and get all those
Blueprint mixins for your stylesheets.

All you have to do is, add the compass gem to your Gemfile:

{% highlight ruby %}
gem 'compass', git: 'https://github.com/chriseppstein/compass.git',
    branch: 'rails31'
{% endhighlight %}

The trick here is that you have to use the compass edge version from git, and
the *rails31* branch currently under development.

This might change in future compass releases, but for now, this is the
only way.

Now, create a *blueprint.css.scss* file on *app/assets/stylesheets* with
the following contents:

{% highlight css %}
  @import '_blueprint';

  @include blueprint;
{% endhighlight %}

This file will just be used to load the blueprint css framework onto our
stylesheet files.

Now, on *app/assets/stylesheets/application.css* make sure you require
the file that you just created by adding it right after *require_self*
in the manifest:

{% highlight css %}
  *= require_self
  *= require blueprint
  *= require_tree .
{% endhighlight %}

And, that's it. Now you'll be able to use all the usual blueprint mixins
on your Rails 3.1 stylesheets.

Hope this helps you somehow.
