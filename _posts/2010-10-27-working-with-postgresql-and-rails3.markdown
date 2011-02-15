---
layout: post
title: Working with PostgreSQL and Rails3
author: Luis Velasco
email: luis.velasco@crowdint.com
avatar: 44db71cea383d8a2203162ed1ecbec35
---

Now, since Rails3 is released, many projects come to be on their phase of migration, but this time I'll show you how I got my Rails environment to work with PostgreSQL.

It's very important to carefully decide which database will be used for your application and what you really care about: Performance, Security, Availability; so in this case we'll just state that we need Security and Availability regardless of Performance, which better approaches us to PostgreSQL. It's important to say that I did this in Ubuntu 10.04 - Lucid Lynx distro.

OK so, let's begin with our brief trip

## Installing Ruby

I'd personally recommend using [RVM](http://rvm.beginrescueend.com/) (Ruby Version Manager) to manage Ruby versions since it's pretty easy to switch, install or remove any version you'd like to test, so let's run:

{% highlight ruby %}
$ rvm install 1.8.7
{% endhighlight %}

and switch to that ruby version by running:

{% highlight bash %}
$ rvm 1.8.7
{% endhighlight %}

## Installing Rails

 When we're ready to use ruby, lets install Rails, if you want, you could install it in any gemset defined in rvm by executing 'rvm gemset create *<gemset_name>*' and 'rvm gemset use *<gemset_name>*', so after this we do:

{% highlight bash %}
$ gem install rails --version 3.0.0
{% endhighlight %}

*Note: if you're using RVM you don't need to use SUDO for Debian based operating systems.*

## Installing PostgreSQL with Visual Administrator

Once we have it on the rails, let's proceed to the main business... Installing PostgreSQL:

{% highlight bash %}
$ sudo apt-get install postgresql postgresql-client postgresql-contrib libpq-dev libpgsql-ruby libgda-4.0-postgres libpq-dev
{% endhighlight %}

 Aditionally you can install *'pgadmin'* visual management tool to make queries, add users or manage any other information from your databases with:

{% highlight bash %}
$ sudo apt-get install pgadmin3 postgresql-doc
{% endhighlight %}

OK, so at this point we have PostgreSQL installed in our system, so... what's next?? Go to sleep? Don't despair! we're near...
 As you can see, we have Rails3 and PostgreSQL installed separately; all we need is an adapter to communicate each other, how do we do it? Easily, just add pg gem to your Gemfile:

{% highlight ruby %}
gem 'pg'
{% endhighlight %}

And run the bundler:

{% highlight bash %}
$ bundle install
{% endhighlight %}

And in your *'config/database.yml'* file use *'postgresql'* as your adapter for each environment.

You could use *'postgres'* as the username, but, I'd recommend using a different one. To create it you'll first have to connect to PostgreSQL server by doing:

{% highlight bash %}
$ sudo -u postgres psql template1
{% endhighlight %}

This will let you into PostgreSQL command line where you can tell the DBMS what you want to do, lets tell it we didn't like that name and create our own:

{% highlight sql %}
CREATE USER <user_name> WITH CREATEDB CREATEUSER ENCRYPTED PASSWORD '<password>';
{% endhighlight %}

I put some words in capital so you can identify the reserved words that PostgreSQL uses and in *<>* tags you can put your own information.
So this will create a user with database and user creation permissions with a defined password.

But if you opted to keep using *'postgres'* user, you can always change the password with:

{% highlight sql %}
ALTER USER postgres WITH ENCRYPTED PASSWORD '<new_password>';
{% endhighlight %}

Note: Don't forget to put *';' (semicollon)* at the end of each instruction in PostgreSQL console

## Configuring our database.yml to use PostgreSQL adapter

And finally, we have to put that information into your *'config/database.yml'*:

{% highlight ruby %}
development:
  adapter: postgresql
  database: <development_database_name>
  username: <user_name>
  password: <password>
  host: 127.0.0.1

test:
  adapter: postgresql
  database: <test_database_name>
  username: <user_name>
  password: <password>
  host: 127.0.0.1

production:
  adapter: postgresql
  database: <production_database_name>
  username: <user_name>
  password: <password>
  host: 127.0.0.1
{% endhighlight %}

And that's it! We can test our connection by creating a model and putting some data in it, but don't forget to *follow the BDD with Cucumber and RSpec cycle!* That's very important!

This is my first post so I'll appreciate any feedback, if there is any problem with the steps or additional help I may give you... Thank you in advance!

