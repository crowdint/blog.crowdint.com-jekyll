---
layout: post
title: Bundler issues with my RubyGems
author: Luis Galaviz
email: luis.galaviz@crowdint.com
avatar: 1e7f8fb8733b6193cf3bdbc85693f515
published: false
---

Last night I was writing some code for a gem I'm building.

For testing purposes I also decided to create a demo application that showed
my new gem behavior. So, I ran the most exciting Rails command I know:

{% highlight bash %}
rails new demo_app
{% endhighlight %}

And, the magic started!

I haven't created a new application in a while, actually, since *Rails 3.0.1*
(so long time ago... I know he he). And now I am using *Rails 3.0.4*, which make
me feel even more excited.

Then I was ready to setup my brand new application. Even tough it was just a demo
application, I decided to drive it in the right way. So, as usual I declared in my
*Gemfile* my testing tools (cucumber, capybara, rspec, selenium) and then I ran:

{% highlight bash %}
bundle install
{% endhighlight %}

Everything was right, so far.

The problems started when I tried to install metrical to measure my good practices
(everybody needs at some feedback). This is what happened:

{% highlight bash %}
ERROR:  Error installing metrical:
  hoe requires RubyGems version >= 1.4.
  Try 'gem update --system' to update RubyGems itself.
{% endhighlight %}

Yes, shame on me, I know. But my RubyGems version was a little bit older. So, I
though that it was a good opportunity to update it.

{% highlight bash %}
gem update --system
{% endhighlight %}

After this update my *RubyGems* version is **1.5.2** and then I successfully installed the metrical gem.

Now I was ready to generate the files needed for my test suite but I noticed a
missing gem. So, I configured it into my Gemfile and I ran again the command bundle install.

Oh, surprise! An error occurred:

{% highlight bash %}
ruby-1.9.2-p0@global/gems/bundler-1.0.7/lib/bundler/ui.rb:56:in
'<class:UI>': uninitialized constant Gem::SilentUI (NameError)
{% endhighlight %}

The problem was that *the bundler gem* was also too old (*1.0.7*) to work with the newest RubyGems
version. I needed to update it, so *gem update* came to my rescue:

{% highlight bash %}
gem update
{% endhighlight %}

And voila! I was ready to rock with **bundler 1.0.10**.

Well, this worked for me, and I hope it works for you as well.

By the way. If you have problems running your cucumber tests because of the builder version, you could
try rather to uninstall the newest builder version (3.0.0) or run this command:

{% highlight bash %}
bundle exec cucumber features
{% endhighlight %}
