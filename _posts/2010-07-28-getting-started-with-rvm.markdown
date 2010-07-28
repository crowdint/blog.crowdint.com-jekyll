---
layout: post
title: Getting started with Ruby Version Manager (RVM)
author: Francisco Guzmán
email: francisco.guzman@crowdint.com
avatar: c000ffd0c4ed3e23d09cae624d24b525
short_date: Jun 28
---

Being a Rails developer brings up the need to work with different Ruby versions, whether it is by a project requirement or to test new features coming out the oven... and the same applies to Rails.

With Rails being in a phase where version 3 is in development, you may (and should) want to take a look to its new features, but surely you don't want to touch your perfectly working Rails 2.3.x installation.

Also, you may be working with Ruby 1.8.x and need to test if your current project works fine with Ruby 1.9.x, so... is there something you can do to have several Ruby mixed with several Rails versions? Chances are you already have heard about Ruby Version Manager (RVM), the amazing tool that will be your best friend when you're dealing with this versions gap.

## Installing RVM

We are going to stick to the most straight-forward method, installing RVM as a user (there's also the option to do it as root and system wide) and from the Github repository. You already work with git, right? If not, check this [post](http://blog.crowdint.com/2010-07-11-git-initial-configuration.markdown).

{% highlight bash %}
bash < <( curl http://rvm.beginrescueend.com/releases/rvm-install-head )
{% endhighlight %}

This is the best option to install RVM in your machine and will work in Macintosh and *nix environments, can't promise anything about Windows, though.

There are other two options: from the latest source tarball and as a gem, but the guys from [BeginRescueEnd](http://rvm.beginrescueend.com/rvm/install/) recommend to stick to the described method.

After RVM is installed in your system, you need to add this line to your ~/.bashrc and ~/.bash_profile

{% highlight bash %}
[[ -s $HOME/.rvm/scripts/rvm ]] && source $HOME/.rvm/scripts/rvm
{% endhighlight %}

## Now what?

Well, you should be ready to start playing around with any Ruby version you want to work with; but before that, it is a good practice to check the Ruby versions that RVM is aware of 

{% highlight bash %}
rvm list known
{% endhighlight %}

To install a specific Ruby version, you just have to:

{% highlight bash %}
rvm install <ruby version>
{% endhighlight %}

When you already have several 'Rubies', you can make any of them the default version:

{% highlight bash %}
rvm --default <ruby version>
{% endhighlight %}

If you ever want (or need) to go back to your previously Ruby/Rails installed version, here's the trick:

{% highlight bash %}
rvm system
{% endhighlight %}

## What about Rails???

The real awesomeness start when you discover that you can install as many Rails versions as you want; and the 'bestest' part is the ability to mix them up with your installed 'Rubies'!!!

Let's take one step at a time.

### Gemsets

RVM provides a feature/concept called 'gemset'; which will allows us to install gems to test them with any of the Ruby versions you have installed.

To create a gemset you have to run:

{% highlight bash %}
rvm gemset create <name for your gemset>
{% endhighlight %}

I like naming my gemsets after the project I will use the Rails version / Gems version; most of the developers will prefer using a naming convention like 'Rails235', 'Rails_3_0_0_beta4', 'Rails3rc' or anything like that.

Now, to mix any of your Ruby version with any of your Rails versions, you'll use:

{% highlight bash %}
rvm use <ruby version>@<gemset name> (e.g.) rvm use 1.8.7@rails_300_RC
{% endhighlight %}

you can omit the word 'use' and it will work too

Now, install the Rails version you want to try within this gemset:

{% highlight bash %}
gem install rails -v 2.3.3
{% endhighlight %}

### Forget about sudo to install gems

This is very important, since you need to install the gems to your current gemset folder, so avoid using sudo or else forget about the magic

## Checking versions

This is pretty straight forward, but make sure to check your Ruby/Rails version each time you switch:

{% highlight bash %}
ruby -v && rails -v
{% endhighlight %}

## What else is important?

Check on the documentation to become an expert using RVM; you can remove Ruby installations, delete-import-export gemsets and a whole bunch of features that will make your life easier (at least working with RoR versions)

Also, the BeginRescueEnd guys have a section for each OS; stop by and take a look.

## Current Ruby selection in your prompt line

There is one interesting post in [RafLabs](http://raflabs.com/blogs/silence-is-foo/2010/07/25/setting-your-terminal-title-to-include-your-current-ruby-version/#comments) about the subject and a very simple implementation by typing:

{% highlight bash %}
PS1=”[\h:\$(~/.rvm/bin/rvm-prompt)]\W \u\$ “
{% endhighlight %}

This will allow your prompt to look a little bit like this:

{% highlight bash %}
[user 1.8.7]

or

[~ ruby-1.9.2-p243]
{% endhighlight %}

## Enjoy!
