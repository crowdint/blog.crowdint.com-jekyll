---
layout: post
title: Budler issues with my RubyGems
author: Luis Galaviz
email: luis.galaviz@crowdint.com
avatar: 1e7f8fb8733b6193cf3bdbc85693f515
---

Last night I was trowing some code for a gem I'm building. For testing purposes,
I decided to create also a demo application that shows my new gem behavior. So,
I ran the most exciting Rails command I know.

{% highlight bash %}
rails new demo_app
{% endhighlight %}

And, the magic started! There was some time since I built a new application,
actually, since Rails *3.0.1* (hehe, so long ago, I know). So, now I was using
Rails *3.0.4* that exciting feeling was double.

Then I was readi to setup my brand new application. Even tough it was just a demo
application, I decided to drive it in the right way. So, as usual I declare in my
*Gemfile* my testing tools (cucumber, capybara, rspec, selenium) and then I ran:

{% highlight bash %}
bundle install
{% endhighlight %}

Everything was right, so far.

The thing started when I tried to install metrical to measure my good practices
(everybody needs at least some feedback). This was what appeared:

{% highlight bash %}
ERROR:  Error installing metrical:
  hoe requires RubyGems version >= 1.4. Try 'gem update --system' to update RubyGems itself.
{% endhighlight %}

Yes, shame on me, I know. But my RubyGems version was a little bit older. So, I
though that was a good opportunity to update it.

{% highlight bash %}
gem update --system
{% endhighlight %}

After this update now my *RubyGems* was on the **1.5.2** version. And then, again, I
successfully tried to install metrical.

Now, I was ready to generate the files needed for my test suite when I noticed a
missing gem. So, I configured it into my Gemfile, and then I ran
again the command bundel install.

Oh, surprise!

{% highlight bash %}
ruby-1.9.2-p0@global/gems/bundler-1.0.7/lib/bundler/ui.rb:56:in `<class:UI>': uninitialized constant Gem::SilentUI (NameError)
{% endhighlight %}

An error occurred. It looks *bundler* was also too old (*1.0.7*) to work with the RubyGems
1.5.2 version. I was required to update it. So, *gem update* came to my rescue. I ran it:

{% highlight bash %}
gem update
{% endhighlight %}

And voila! I was ready to rock with **bundler 1.0.10**.

Well, this worked for me, and I hope it works for you as well.
