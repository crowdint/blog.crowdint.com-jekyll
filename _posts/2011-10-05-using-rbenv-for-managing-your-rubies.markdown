---
layout: post
title: Using rbenv to manage your rubies
author: Ignacio Galindo
email: ignacio.galindo@crowdint.com
avatar: 6be5df410f2695b1341f0c359bc9b461
---

While working with ruby, you have some alternatives to manage your binaries,
but as you think of them, I hope you are not really considering delegating this
task directly to your OS package manager since it is discouraged, otherwise you
would end up with a messy workstation.

There are a few tools that allow you to manage your rubies and gems, the most
popular among them is without doubts [RVM](http://beginrescueend.com/)
(Ruby Version Manager) which in all fairness is good, it provides a CLI to
switch between your rubies and gemsets. If you haven't used RVM, you should
read [this](http://blog.crowdint.com/2010/07/28/getting-started-with-rvm.html).

Even when RVM is great there are a couple things that I don't like about it:

* Personally, I had a painful situation as Linux user, every time I wanted to
  get a fresh ruby version with support for readline and zlib libraries and its
  dependencies.

* Its gemset management feature tends duplicate gems across your projects. I
  get it, sometimes you need to isolate your gems to keep them compatible. But
  there is another player in the field, called bundler. (mention down below)

* Plus that last bullet, some colleages have mentioned having a 5~6 GB .rvm
  folder.

A couple days ago, I was struggling tracking down a gem that I wasn't sure
where exactly came from, anyway I was about to create a new gemset for a fresh
started when someone adviced me to check out rbenv.

## The rbenv way

A highlight in favor of rbenv is that you don't actually need to worry about
maintaing your gemsets, since it relays on [bundler](http://gembundler.com/)
who takes care of all your application dependencies. Letting you care about
just the version of the ruby you want to use globally, locally and in a per
project basis. Let's check out [rbenv](http://gituhub.com/ssthepenson/rbenv).

*RVM and rbenv aren't friends :(*

First of all, you better avoid using both in the same environment because they
are incompatible. Don't say I didn't warn you.

## Installation

### 1. Get rid of RVM by running:

{% highlight bash %}
$ rvm implode
{% endhighlight %}

### 2. To install rbenv, must be at ~ and clone it:

{% highlight bash %}
$ git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
{% endhighlight %}

### 3. Add scope for rbenv binaries to your $PATH

{% highlight bash %}
$ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> .bash_profile
# be sure of do this to your bash source file (e.g. .bashrc, .profile)
{% endhighlight bash %}

### 4. Setup bash autocompletion:

{% highlight bash %}
$ echo 'eval "$(rbenv init -)"' >> .bash_profile
{% endhighlight bash %}

### 5. Restart your shell.

{% highlight bash %}
$ exec
{% endhighlight bash %}

There are two ways of installing rubies with rbenv. From source and *make it*
into "~/.rbenv/versions/<x.x.x-pxxx>" or the one I prefer using *ruby-build*

### 6. Install ruby-build
{% highlight bash %}
$ git clone git://github.com/sstephenson/ruby-build.git ~/.ruby-build
$ cd ~/.ruby-build
$ ./install.sh
# you may need to run with sudo, since it installs a binary in /usr/local/bin
{% endhighlight bash %}

### 7. Install a ruby

Now, we are ready to install a ruby.

*Note:* After a couple times trying to get a ruby with readline support
for my *irb*, I googled and found a way.

*For Ubuntu* I used my readline path:

{% highlight bash %}
$ CONFIGURE_OPTS="--with-readline-dir=/usr/include/readline" rbenv
install 1.9.3-preview1
{% endhighlight bash %}

And there we go, we give it some time, get a coffee or play a ping pong
match. Once it rbenv finishes, and every time after installing a ruby
you need to run:

{% highlight bash %}
$ rbenv rehash
{% endhighlight bash %}

I have to mention, that it seems tricky, but you can set an alias in
your `~/.bash_profile` or export an environment variable.

## Usage

Let's suposse you've got some more rubies, now, how do we specify the
version of ruby we want to use:

*To setup a global ruby you do something like:*

{% highlight bash %}
$ rbenv global 1.9.3-preview1
{% endhighlight bash %}

*To setup a local (per-project) ruby you do:*

{% highlight bash %}
$ rbenv local 1.9.2-p290
# this creates a rbenv-version file in the current folder
{% endhighlight bash %}

*What version of ruby am I using?*
{% highlight bash %}
$ rbenv version
{% endhighlight bash %}


*What versions of ruby do I have?*
{% highlight bash %}
$ rbenv versions
{% endhighlight bash %}


## Conclusions

There are a couple things that remain unexplored, but for now this is a getting
started. In case you miss the gemset, [this](http://github.com/jamis/rbenv-gemset)
is something you might want to look at.

So, give it a try, you might like it. For now I'm happy with my fresh
rbenv install. Let us know your rbenv experience.
