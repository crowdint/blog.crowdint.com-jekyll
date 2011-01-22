---
layout: post
title: How to start writing a ruby gem
author: David Padilla
email: david@crowdint.com
avatar: d32b52ec6403614b1adf3e648cbbe584
published: true
---

If you are a Ruby developer, by now, you have probably used tons of
gems in your apps.

That's one of the best things of using Ruby, a lot of people writes
repeatable code that you can easily integrate into your own apps.

In this brief tutorial, I will try to explain the basic things you need
to know in order to start writing your own gems, you know, just in case
you have something to share with the world.

## Setting up the folders

We'll start by creating the gem folder structure using Bundler.

First thing you have to do is, install bundler itself. Easy.

{% highlight bash %}
$ gem install bundler
{% endhighlight %}

Now, bundler comes with a handy command to generate the basic files to
start writing a gem.

{% highlight ruby %}
$ bundler gem awesome_gem

  create  awesome_gem/Gemfile
  create  awesome_gem/Rakefile
  create  awesome_gem/.gitignore
  create  awesome_gem/awesome_gem.gemspec
  create  awesome_gem/lib/awesome_gem.rb
  create  awesome_gem/lib/awesome_gem/version.rb
{% endhighlight %}

## The Gemspec

All gems have a *.gemspec* file. This files contains all the info on your
gem. The name, a description, dependencies. Let's open it and see how it
looks.

{% highlight ruby %}
require "awesome_gem/version"

Gem::Specification.new do |s|
  s.name        = "awesome_gem"
  s.version     = AwesomeGem::VERSION
  s.platform    = Gem::Platform::RUBY
  s.authors     = ["TODO: Write your name"]
  s.email       = ["TODO: Write your email address"]
  s.homepage    = ""
  s.summary     = %q{TODO: Write a gem summary}
  s.description = %q{TODO: Write a gem description}

  s.rubyforge_project = "awesome_gem"

  s.files         = `git ls-files`.split("\n")
  s.test_files    = `git ls-files -- {test,spec,features}/*`.split("\n")
  s.executables   = `git ls-files -- bin/*`.split("\n").map{ |f| File.basename(f) }
  s.require_paths = ["lib"]
end
{% endhighlight %}

Now, all you have to do is replace some of the fields here with your own
information.

The only things that is not explicitily given is a place to define
dependencies.

Let's say your gem heavily depends on *rails*. Then, you have to add a
line to state so:

{% highlight ruby %}
s.add_dependency('rails', '3.0.0')
{% endhighlight %}

You use shoulda or mocha? Since you use them only to test it while
developing, you define it as a *development dependency*.

{% highlight ruby %}
s.add_development_dependency('shoulda')
s.add_development_dependency('mocha')
{% endhighlight %}

So, our .gemspec file ends up loking like this:

{% highlight ruby %}
require "awesome_gem/version"

Gem::Specification.new do |s|
  s.name        = "awesome_gem"
  s.version     = AwesomeGem::VERSION
  s.platform    = Gem::Platform::RUBY
  s.authors     = ["David Padilla"]
  s.email       = ["david@crowdint.com"]
  s.homepage    = ""
  s.summary     = %q{A gem that will change the world}
  s.description = %q{A long description for a gem that will change the world}

  s.rubyforge_project = "awesome_gem"

  s.files         = `git ls-files`.split("\n")
  s.test_files    = `git ls-files -- {test,spec,features}/*`.split("\n")
  s.executables   = `git ls-files -- bin/*`.split("\n").map{ |f| File.basename(f) }
  s.require_paths = ["lib"]

  s.add_dependency('rails', '3.0.0')
  s.add_development_dependency('shoulda')
  s.add_development_dependency('mocha')
end
{% endhighlight %}

The best thing about this is that now, you can just run the *bundler
install* command, and you will get the gem dependencies installed on your
computer.

{% highlight bash %}
$ bundler install

  Installing rails (3.0.0)
  Using mocha (0.9.10)
  Using shoulda (2.11.3)
  ...
{% endhighlight %}

### The code

By definition, you have a file called *lib/THE_NAME_OF_YOUR_GEM.rb* that
will be called when you require your gem on any piece of ruby code.

Usually, your gem will have a lot of code spread in Modules and Classes.
This file should only work as the glue to put everything together.

Let's say your gem introduces a new class called *Mushroom*. You will
have to create a *lib/mushroom.rb* file with the class definition:

{% highlight ruby %}
class Mushroom
  # Some code
  ...
end
{% endhighlight %}

And then, require it on *lib/awesome_gem.rb*

{% highlight ruby %}
require 'mushroom'
{% endhighlight %}

And so on. Obviously, you will probably want to put the classes and
their files on a namespace hierarchy. Create folders for each namespace.

So, if for example, you had some classes that are used to access
different stuff, you want to create an Accessor namespace and put all
the classes on the *lib/accessors* folder.

{% highlight ruby %}
class Accessor::TextFileAccessor

end
{% endhighlight %}

From here on, you have to rely on *Ruby Metaprogramming*, but I'll leave
that for another post.

## Unit tests

To include some rake tasks to test your gem, add the following lines to
*Rakefile*.

{% highlight ruby %}
require 'rake/testtask'
Rake::TestTask.new(:test) do |test|
  test.libs << 'lib' << 'test'
  test.pattern = 'test/**/test_*.rb'
  test.verbose = true
end
{% endhighlight %}

This will add a *rake test* rake task that will run all the tests that
you define on the *test* folder.

You can also install rspec, bacon, minitest or any test framework of
your choice. Read each of their documentation to find out how to add
them to your gem.

## Testing your code on an existing app

Obviously, to test your gem, you'd use something like *rspec* or
*minitest*, but, just in case you want to see how it will end up working
on an existing app that uses Bundler, you can add it to the other's app
Gemfile and specify the local path to use the local version of the gem
instead of downloading it from a repo:

{% highlight ruby %}
gem 'awesome_gem', :path => '/Users/some_path/awesome_gem'
{% endhighlight %}

This way, any change you make on the gem will be immediately reflected
on the app where you installed it.

## Deploying to rubygems.org

Once you are done writing some awesome ruby code, it's time to deploy
your app for everyone to use.

All you have to do is run

{% highlight bash %}
$ rake release
{% endhighlight %}

And this will publish your gem to rubygems.org.

## Version

To have control over hav your gem changes overtime, you want to use a
version.

Your gem's version is defined on the *lib/awesome_gem/version.rb* file.

If you open it, you'll see that there's only one line on the file:

{% highlight ruby %}
module AwesomeGem
  VERSION = "0.0.1"
end
{% endhighlight %}

Remember this line on the gemspec?

{% highlight ruby %}
s.version     = AwesomeGem::VERSION
{% endhighlight %}

This means that, the version defined on the gemspec is being pulled from
this file.

It also means that you can access the version of the gem from anywhere
in the code, even on the code where you are using the gem.

So, you can do things like:

{% highlight ruby %}
if AwesomeGem::VERSION == '1.0.0'
  # Do it this way for verision 1.0.0
else
  # For all the other versions do it that way
end
{% endhighlight %}

This, of course, is not recommended and should be only used in case
where there's no other solution, or, to display deprecation messages.

## Conclusion

Before writing a gem, make sure you google around and browse github for
the behavior that you are looking for. Most of the times, someone already
thought of a generic solution for your problem. Sometimes you won't
like that solution and you'll end up writing your own gem, sometimes
you will like it and use it.

Contributing is what keeps the community alive, write lots of gems!

Hope this points you in the right direction.

