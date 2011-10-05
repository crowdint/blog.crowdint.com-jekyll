---
layout: post
title: Rails 3.1, Compass and Blueprint (Updated)
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
group :assets do
  gem 'compass', '~> 0.12.alpha'
end
{% endhighlight %}

*NOTE:* If you want the scss files compiled on the fly, you have to put
the compass gem line *outside of the assets group*.

The trick here is that you have to use the latest alpha version of compass <span class="updated">compass edge version from git, and
the *rails31* branch currently under development</span>.

<span class="updated">This might change in future compass releases, but for now, this is the
only way.</span>

## Blueprint styles

If you just want to use the blueprint classes on your application, create a
*blueprint.css.scss* file on *app/assets/stylesheets* with
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

And, that's it. Now you'll be able to use all the usual blueprint styles
on your Rails 3.1 application.

## Mixins

If you want to use the blueprint mixins, you must do something a bit
different than the regular asset pipeline stuff.

Let's say you will have a bookstore.css file with all your styles. If
this is the case, create a file named
_app/assets/stylesheets/bookstore.css.scss_ with the following contents:

{% highlight css %}
/*
  *= require_self
  *= require blueprint
*/

@import 'bookstore/*';

{% endhighlight %}

This way, all the css.scss on your _app/assets/stylesheets/bookstore_
will be compiled into bookstore.css.

### Why not just use require_tree ./bookstore?

You might think that it's better to use require_tree for this, but, since the asset
pipeline compiles each individual css file for development, all of them
would have to be headed with the 'require blueprint' line, else, it will
show you an error saying that it doesn't recognize the mixins on each
file.

Hope this helps you somehow.
