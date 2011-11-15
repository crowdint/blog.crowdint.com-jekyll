---
layout: post
title: Making gems with jeweler
author: Victor Velázquez
email: victor.velazquez@crowdint.com
avatar: 8a4d383333234bd653b7d768ee999c11
---

A few days ago I was just wondering what the best way to craft a
[Gem](http://en.wikipedia.org/wiki/RubyGems) was, and I
figured out it doesn't really matter. Any way you take will lead you to the same place, yet,
I really liked an special way to do it: I found [JEWELER](https://github.com/technicalpickles/jeweler).
In short, Jeweler is a GEM to make GEMS, great, right?

Its quite simple to use, you just need to follow a few steps to get the skeleton
of a GEM and then you will get it ready to use.

The greatest stuff about Jeweler is that you have a library to manage and release
[RubyGem](http://en.wikipedia.org/wiki/RubyGems) projects. Don't forget to create
your account on the RubyGems website, that will be your signature like a gem crafter.

You can also use its scaffold generator to start a new RubyGem project.


### We need to use RubyGems to install JEWELER and get started:

{% highlight bash %}
$ gem install jeweler
{% endhighlight %}


### Now with JEWELER installed we are ready to start building our own gem

{% highlight bash %}
$ jeweler <my-gem-name>
{% endhighlight %}

for instance:

{% highlight bash %}
$ jeweler My-Gem
{% endhighlight %}

You'll get this output:

{% highlight bash %}
  create  .gitignore
  create  Rakefile
  create  Gemfile
  create  LICENSE.txt
  create  README.rdoc
  create  .document
  create  lib
  create  lib/My-Gem.rb   #Here you need to put your code
  create  test       #Here you need to put your Unit Test code
  create  test/helper.rb
  create  test/test_My-Gem.rb
{% endhighlight %}

This means now you have the skeleton for your gem in the My-Gem directory.

If you want to see the JEWELER command options just run jeweler --help

### Then we need rake commnad to interact with our gem

This step is really important because with this you can generate and validate
the gemspec, realease the gem, run its test suite, display the current version, etc.

{% highlight bash %}
$ rake -T

  rake build              # Build gem into pkg
  rake clobber_rcov       # Remove rcov products for rcov
  rake clobber_rdoc       # Remove rdoc products
  rake console[script]    # Start IRB with all runtime dependencies loaded
  rake gemcutter:release  # Release gem to Gemcutter
  rake gemspec            # Generate and validate gemspec
  rake gemspec:debug      # Display the gemspec for debugging purposes, as jeweler
  rake gemspec:generate   # Regenerate the gemspec on the filesystem
  rake gemspec:release    # Regenerate, validate gemspec.
  rake gemspec:validate   # Validates the gemspec on the filesystem
  rake git:release        # Tag and push release to git.
  rake install            # Build and install gem using `gem install`
  rake rcov               # Analyze code coverage with tests
  rake rdoc               # Build the rdoc HTML Files
  rake release            # Release gem
  rake rerdoc             # Force a rebuild of the RDOC files
  rake test               # Run tests
  rake version            # Displays the current version
  rake version:bump:major # Bump the major version by 1
  rake version:bump:minor # Bump the a minor version by 1
  rake version:bump:patch # Bump the patch version by 1
  rake version:write      # Writes out an explicit version.
{% endhighlight %}

### Now we need to set a version before we start using the gem

{% highlight bash %}
$ rake version:write MAJOR=1 MINOR=0 PATCH=0
{% endhighlight %}

The small explanation for this is: if you do a small change you need to
increase PATCH (0 - 9) then you will start to increase MINOR (0 - 9) stage, and
when you get MAJOR changes, its because you did a whole new version of your gem.

You will see this message:

{% highlight bash %}
Updated version: 1.0.0
{% endhighlight %}

### Then, we need to edit our Rakefile and change a couple of lines inside, I suggest to you use VIM/MVIM/GVIM to do that:

{% highlight bash %}
$ vim Rakefile
{% endhighlight %}


If your gem depends upon other libraries, you'll need to define a gem specification, commonly called a "gemspec".

### Inside Rakefile look for "gem" and that is the Gem Specification Reference.

Here you need to change:

{% highlight bash %}
$ gem.summary = %Q{TODO: summary here about your gem}
$ gem.description = %Q{TODO: longer description of your gem}
{% endhighlight %}

  to this:

{% highlight bash %}
$ gem.summary = %Q{Writte a summary here about your gem}
$ gem.description = %Q{Writte a longer description of your gem}
{% endhighlight %}


### After this, we'll install our gem locally and so, we can start testing it:

{% highlight bash %}
$ rake install
{% endhighlight %}


### If later you want to release your gem into www.rubygems.org you just need to run the command:

{% highlight bash %}
$ rake release
{% endhighlight %}

This will automatically:
* Generate My-Gem.gemspec and commit it
* Use git to tag v1.0.0 and push it
* Build My-Gem-1.0.0.gem and push it to rubygems.org

## Conclusions

Now you to know about use jeweler, use it and see how this works, its
not the only way to do a gem, it just works for me, but you can find your own way to do
it however you feel more confortable.

*Make your gem and release it* to RubyGems, everybody is waiting for your sexy gems to
make their developer life easier.


>"Easy way to make yours owns gems (FEB). Faster, Easy and Beautiful"
