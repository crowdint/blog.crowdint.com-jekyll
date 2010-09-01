---
layout: post
title: Customize your Generators Workflow in Rails 3.0.0
author: Luis Galaviz
email: luis.galaviz@crowdint.com
avatar: 1e7f8fb8733b6193cf3bdbc85693f515
---

When we talk about a concept like DRY, we usually associate it with coding (partials, controllers, models, helpers, rakes, automatic deploys, etc.),and all other stuff. Usually, we don't realize that what we do on our day by day like when we create new layers, controllers, test fixtures, etc. we create also (sometimes) a big source of waste.

We have to remember that DRY concept was conceived with the idea of avoid as much waste as we can. So, why do we need to waste time creating separately what we need, for example, when we create a new controller, or model?

Fortunately, Rails 3 provides an easy way to configure our generators and make it easy for us to customize them, even for chose customize the workflow. So, let's start an example:

## Hands on!

First, we need to create a new Rails Project, we could use as always

{% highlight bash %}
rails new g_test
{% endhighlight %}

In our case, we will create this avoiding Test::Unit because we will use Cucumber and RSpec, we are going to use something like this:

{% highlight bash %}
rails new g_test -T
{% endhighlight %}

Ok, now we can configure our Gemfile, we need set haml, cucumber, capybara and rspec gems:

{% highlight bash %}
group :test do
  gem "rspec-rails", ">= 2.0.0.beta.19"
  gem 'cucumber-rails'
  gem 'capybara'
end

gem 'haml-rails'
{% endhighlight %}

Then, we just need to install our gems to star to play with our generators:

{% highlight bash %}
bundle install
{% endhighlight %}

And, that's it! Now we have a perfect (almost) environment to begin with.

## Generators

Now, let's invoke our a Rails generator to create a new controller called Test:

{% highlight bash %}
rails g controller Test
{% endhighlight %}

This function is just going to invoke many generators, to create something like this:

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

As you can see, this generator is still invoking Test::Unit as our test environment, but what we need is to invoke Rspec, so let's change our generators workflow. First we need to install Rspec and Cucumber:

{% highlight bash %}
rails g cucumber:install
rails g rspec:install
{% endhighlight %}

Then, just add this lines to our '~/application.erb'

{% highlight bash %}
config.generators do |g|
  g.test_framework :rspec
end
{% endhighlight %}

If we try to generate a new controller called RspecTest something like this will appear:

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

Also, we could specify that we don't want to create view specs just adding ':views => false' like this:

{% highlight bash %}
g.test_framework :rspec, :views => false
{% endhighlight %}


