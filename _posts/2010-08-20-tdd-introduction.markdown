---
layout: post
title: Agile/Bundler small test App
author: Emmanuel Delgado
email: emmanuel.delgado@crowdint.com
avatar: a302e7dd208f335dc67761a6db911561
---

This guide is intended to show how Bundler helps you keep organized your gem dependencies.

Think about the following scenarios when working with Ruby and requiring gems:
* You have to change some gem versions due some unfixed bug
* One of your teammates has a different gem version than yours
* Your project depends on multiple gems which at the same time have many other dependencies
* Your project has different environments and each environment has different gems  

Well Bundler brings you some work arounds to this common issues and takes things further by 
introducing some clever tricks to organize you project dependencies.

Let's create a simple project whose purpose will be retrieve a list of your gmail contacts, this 
project will have two environments: test and production.

Let's start by creating our project skeleton:
{% highlight bash %}
mkdir -p gcontacts/lib gcontacts/test gcontacts/bin
touch gcontacts/lib/gcontacts.rb gcontacts/test/gcontacts_test.rb gcontacts/bin/gcontacts
{% endhighlight %}

And now let's create our test *gcontacts/test/gcontacts_test.rb*:
{% highlight ruby %}
class GContactsTest < Test::Unit::TestCase
  def setup
    @gcontacts = GContacts.new('USER', 'PASSWD')
  end
  def test_contains_expected_methods
    assert_true(@gcontacts.methods.include?('retrieve_all'))
  end  
  def test_retrieve_all
    assert_true(@gcontacts.retrieve_all.is_a?(Array))
  end
end
{% endhighlight %}

This empty test with fail with:
{% highlight bash %}
ruby gcontacts/test/gcontacts_test.rb
gcontacts/test/gcontacts_test.rb:1: uninitialized constant Test (NameError)
{% endhighlight %}

OK so we need our testing framework, so I am going to use Test::Unit, and introduce Bundler,
first lets install bundler:
{% highlight bash %}
gem install bundler
{% endhighlight %}

Now bundler allows you to change your gems source so they might come from gemcutter, rubygems, even
git repositories, all gem dependencies are declared on one single *Gemfile* file, we are going to
add Test/Unit, and since we only require it for test will put it inside :test group:
{% highlight bash %}
touch gcontacts/Gemfile
{% endhighlight %}
Then, edit *Gemfile*
{% highlight ruby %}
source :gemcutter
gem 'test-unit', :group => 'test'
{% endhighlight %}

Once you have that file you only need to install you dependencies through:
{% highlight bash %}
cd gcontacts
bundle install
Fetching source index from http://gemcutter.org/
Using test-unit (2.1.1) from bundler gems 
Your bundle is complete! Use `bundle show [gemname]` to see where a bundled gem is installed.
{% endhighlight %}

Bundle is now installed so we are back in track, now we need to include this dependencies in top of our
test case, so lets modify it:

{% highlight ruby %}
require 'bundler'
Bundler.setup(:test)
require 'unit/test'
{% endhighlight %}

And now we have to run our tests:
{% highlight bash %}
ruby test/gcontacts_test.rb 
Loaded suite test/gcontacts_test
Started
EE

  1) Error:
test_contains_expected_methods(GContactsTest):
NameError: uninitialized constant GContactsTest::GContacts
    test/gcontacts_test.rb:8:in `setup'

  2) Error:
test_retrieve_all(GContactsTest):
NameError: uninitialized constant GContactsTest::GContacts
    test/gcontacts_test.rb:8:in `setup'

Finished in 0.005571 seconds.

2 tests, 0 assertions, 0 failures, 2 errors, 0 pendings, 0 omissions, 0 notifications
0% passed
{% endhighlight %}

Good, our tests failed since we do not have our GContacts coded and required, so lets write the 
minimum code for out tests to pass, edit *lib/gcontacts.rb*:
{% highlight ruby %}
class GContacts
  def initialize(user, password)
  end
  def retrieve_all
    []
  end
end
{% endhighlight %}

And we need to include our project's lib in our *test/gcontacts_test.rb* so we have to add this on
top of it:
{% highlight ruby %}
$LOAD_PATH << File.expand_path('../../lib' , __FILE__)
require 'gcontacts'
{% endhighlight %}

And re-run our tests, which should pass:
{% highlight bash %}
ruby test/gcontacts_test.rb 
Loaded suite test/gcontacts_test
Started
..

Finished in 0.004691 seconds.

2 tests, 2 assertions, 0 failures, 0 errors, 0 pendings, 0 omissions, 0 notifications
100% passed
{% endhighlight %}

Now we need to create the application that will retrieve a list of our contacts using our brand 
new GContacts class, we are going to create a simple command line user, password input system, 
so we add following to *bin/gcontacts*:
{% highlight ruby %}
#!/usr/bin/env ruby
$LOAD_PATH << File.expand_path('../../lib' , __FILE__)
require 'rubygems'
require 'bundler/setup'
require 'gcontacts'

gcontacts = GContacts.new *ARGV
p gcontacts.retrieve_all
{% endhighlight %}

I know this command line application sucks but feel free to improve it, I would
suggest you either google it or read this [post](http://blog.infinitered.com/entries/show/5) 
from *Todd Werth*.

Let's try out the cli application:
{% highlight bash %}
chmod 777 bin/gcontacts
bin/gcontacts username password
[]
{% endhighlight %}

Yeah our awesome app is alive!... no, we are not retrieving anything yet, we will work on
that later before finishing this post.

Lets review the most important points so far:
1. You declare all your project gem dependecies, groups and sources inside a single Gemfile
2. If you want some script/classes/files to take into account your gem dependencies use:
  1. require 'bundler/setup'
  2. require 'bundler'
    Bundler.setup or Bundler.setup(:group1, :group2)
  3. There is another approach that can help you remove some code duplication by taking off from your 
  files rubygems and bundler requires, this is achieved using bundle cli. Find more related information 
  on [gembundler site](http://gembundler.com/) or in [this post](http://gluei.com/blog/view/getting-bundlersetup-bundlerrequire-and-bundle-exec-to-play-nicely-together), actually that last link helped me understand a lot of things.
3. Bundler.setup loads all gems in :default group, default group gems are all the ones not
included in any other group
4. We are creating our example using a basic TDD red-green-refactor cycle approach.

OK, let's finish this, we need to connect to Google Contacts API
