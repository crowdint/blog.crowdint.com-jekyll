---
layout: post
title: Sinatra, the green way
author: Omar Vargas
email: omar@crowdint.com
avatar: 433031df4018635dfb177f4066c37a3b
---

Sinatra is one of the most popular micro frameworks for Ruby, it’s an easy and fast way to create web applications or services.

On the other hand, Cucumber is a powerful tool to create user stories and acceptance tests.
Together they are a quick and efficient way to create web applications.

In this post we'll create our first Sinatra application using 'outside-in' development with
Cucumber or how I like to call it, *the green way*.

## First Sinatra application.

First of all, we need to create our basic environment. Personally, I like to use a gemset per project.

Then we need to create our application's directory. Let’s call it ‘greenway’. Inside the ‘greenway’ directory,
call bundle init (assuming you already installed bundler) and then edit the Gemfile so that it looks
like the example below:

{% highlight ruby %}
  source 'http://rubygems.org'

  gem 'sinatra'
  gem 'cucumber'
  gem 'cucumber-sinatra'
  gem 'capybara'
  gem 'rspec'
{% endhighlight %}

> With rvm you can use diferents rubies and gemsets for each project. If you don't know rvm. What are you waiting for?
go to [RVM](http://rvm.beginrescueend.com/).
For more info on gemsets and RVM check out these posts: [getting started with rvm](http://blog.crowdint.com/2010/07/28/getting-started-with-rvm.html) [Use a project specific Ruby version](http://blog.crowdint.com/2010/08/17/use-a-project-specific-ruby-version-rvm.html)

Now we can install our gems, with this command
{% highlight bash %}
  $ bundle install
{% endhighlight %}

If you haven’t installed Bundler, you can do so with:
{% highlight bash %}
  $ gem install bundler
{% endhighlight %}

Now we need to create our Sinatra application, set the configuration for Cucumber, define the web steps and so on.
To start we have to install the cucumber-sinatra gem, it does most of the configuration work for us.
For more info visit [cucumber-sinatra](https://github.com/bernd/cucumber-sinatra)
{% highlight bash %}
  $ cucumber-sinatra init --app Hello src/hello.rb
{% endhighlight %}

### Writing tests

Now we can write our cucumber test... yes, I said test.

Inside the features directory create a new file called hello.feature.

    {% highlight ruby %}
        Feature: Cucumber web testing
          In Order to verify if my app works
          As A developer
          I want to see my home page

          Scenario: View hello page
            Given I am on the home page
            Then I should see "It's alive!"
    {% endhighlight %}

Then run the test:
{% highlight bash %}
  $ cucumber feature/hello.feature
{% endhighlight %}

![Red tests](/images/sinatra_green_way/sinatra_red_tests.jpg)

And... it Fails! But don’t worry, that was expected. Now we are ready to write the code we need to make that test pass.

### Writing Code

This is the moment to write the code to pass the test. Open the file src/hello.rb and edit it to look like the following:

    {% highlight ruby %}
        require 'sinatra/base'

        class Hello < Sinatra::Base
          get '/' do
            "It's alive!"
          end
        end
    {% endhighlight %}

And now, run the test again

{% highlight bash %}
  $ cucumber feature/hello.feature
{% endhighlight %}

![Green tests](/images/sinatra_green_way/sinatra_green_tests.jpg)

Now we're green. If you want to see your web application, you can do it with:
{% highlight bash %}
  $ rackup -p 4567
{% endhighlight %}

And you just have to go to "localhost:4567/" in your browser

Congrats for your first "green way" Sinatra application! To celebrate, I’ll leave you with [Frank](http://www.youtube.com/watch?v=Aht9hcDFyVw)

## References
[Cucumber](http://cukes.info/) Behavior Driven Development tool.

[Siantra](http://www.sinatrarb.com/) a DSL for quickly creating web applications in Ruby.

[Cucumber-Sinatra](https://github.com/bernd/cucumber-sinatra) helpful gem to initialize a cucumber environment for a sinatra application.

[RVM](http://rvm.beginrescueend.com/) Command line tool to easily install, manage and work with multiple ruby environments.

[Bundler](http://gembundler.com/) a tool that manages gem dependencies for your ruby application.
