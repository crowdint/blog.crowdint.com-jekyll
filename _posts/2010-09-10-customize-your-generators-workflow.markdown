---
layout: post
title: Customize your Generators Workflow in Rails 3.0.0
author: Luis Galaviz
email: luis.galaviz@crowdint.com
avatar: 1e7f8fb8733b6193cf3bdbc85693f515
---

When we talk about a concept like DRY, we usually associate it with coding (partials, controllers, models, helpers, rakes, automatic deploys, etc.), and all that other stuff. We don't usually realize that, what we do on our day by day, like, when we create new layers, controllers, test fixtures, etc. we create (sometimes) a big source of waste.

We have to remember that the DRY concept was conceived with the idea to avoid as much waste as we can. So, why do we need to waste time creating all we we need by separate, for example, when we create a new controller, or model?

Fortunately, Rails 3 provides an easy way to configure your generators and make it easy for us to customize them, you can even customize their workflow.

So, let's try an example:

## Hands on!

First, we need to create a new Rails Project:

{% highlight bash %}
rails new g_test
{% endhighlight %}

In our case, we will exclude Test::Unit because we will use Cucumber and RSpec. You can easily do that with the -T option:

{% highlight bash %}
rails new g_test -T
{% endhighlight %}

Ok, now we can configure our Gemfile. We need haml, cucumber, capybara and rspec:

{% highlight bash %}
group :test do
  gem "rspec-rails", ">= 2.0.0.beta.19"
  gem 'cucumber-rails'
  gem 'capybara'
end

gem 'haml-rails'
{% endhighlight %}

Then, we just need to install our gems to start playing with our generators:

{% highlight bash %}
bundle install
{% endhighlight %}

And, that's it! Now we have a perfect (almost) environment to work with.

## Generators

Now, let's invoke a Rails generator to create a new controller called Test:

{% highlight bash %}
rails g controller Test
{% endhighlight %}

This function is just going to invoke many other generators, to create something like this:

{% highlight bash %}
create  app/controllers/test_controller.rb
invoke  haml
create    app/views/test
invoke  test_unit
create    test/functional/test_controller_test.rb
invoke  helper
create    app/helpers/test_helper.rb
invoke    test_unit
create      test/unit/helpers/test_helper_test.rb
{% endhighlight %}

As you can see, this generator is still invoking Test::Unit as our test environment, but what we want is to invoke Rspec, so let's change our generators workflow. First we need to install Rspec and Cucumber on our project:

{% highlight bash %}
rails g cucumber:install
rails g rspec:install
{% endhighlight %}

Then, just add these lines to our (<del>'~/application.erb'</del>) '~/application.rb' (Thanks to Bernardo466)

{% highlight bash %}
config.generators do |g|
  g.test_framework :rspec
end
{% endhighlight %}

If we try to generate a new controller called RspecTest something like this will show up:

{% highlight bash %}
~$ rails g controller RspecTest
create  app/controllers/rspec_test_controller.rb
invoke  haml
create    app/views/rspec_test
invoke  rspec
create    spec/controllers/rspec_test_controller_spec.rb
invoke  helper
create    app/helpers/rspec_test_helper.rb
invoke    rspec
create      spec/helpers/rspec_test_helper_spec.rb
{% endhighlight %}

We could also specify that we don't want to create view specs just by adding ':views => false' in our '~/application.rb' like this:

{% highlight bash %}
g.test_framework :rspec, :views => false
{% endhighlight %}

Now what? Do you want to create your own generator? Generators, can even generate generators (sounds like a tongue twisters), check this out:

{% highlight bash %}
~$ rails g generator my_test_method
create  lib/generators/my_test_method
create  lib/generators/my_test_method/my_test_method_generator.rb
create  lib/generators/my_test_method/USAGE
create  lib/generators/my_test_method/templates
{% endhighlight %}

This generator creates the files you need to start working. How does it work? Well, it's simple. Basically a directory named "generators" is created in your lib folder and, 'my_generator'_generator.rb is where you will put your generator logic, USAGE describes what your generator makes and 'templates' is where you will put you generator templates.

What commands can you use inside your generator? Rails 3 generators were created on top of [Thor](http://github.com/wycats/thor), so you could check Thor [documentation](http://rdoc.info/github/wycats/thor/master), and also there is a great document you can follow on [RailsGuides](http://edgeguides.rubyonrails.org/generators.html).

Anyway, if you want to know more about an specific generator, you could use this command:

{% highlight bash %}
rails g [generator_name] -h
{% endhighlight %}

There is a list of generators that Rails can handle, also there are others hidden in many gems. If you wan to obtain a lot of helpful generators, I'd recommend you check the [rails3-generators](http://github.com/indirect/rails3-generators) gem, there are many helpful generators that can make your coding more DRY.

By the way, there are other things I can't make them to do. For example, if I try to use "my_test_method_generator" as follows, I get an error:

in "application.rb":
{% highlight bash %}
g.helper :myhelper
{% endhighlight %}

in you terminal:
{% highlight bash %}
~$ rails g controller [name]
...
error  myhelper [not found]
{% endhighlight %}

However, generators can really help you when you try to be as DRY as possible. So, try it, you might like it.

