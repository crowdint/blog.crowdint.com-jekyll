---
layout: post
title: A simple way to setup a class for global values
author: Francisco Guzm&aacute;n
email: francisco.guzman@crowdint.com
avatar: c000ffd0c4ed3e23d09cae624d24b525
---

Most everytime we are working on a Rails app, we face the need to set up config values for said app; this is a good practice to avoid hardcoded lines. For example, think of the Application Name value... if you had to use it over a hundred times in your views (including mailers), wouldn't you want it to be called from a class so if your app name's ever changes, well... you wouldn't have to search and replace it.

## Setting up the class

It couldn't be more simple. We just have to create a ruby file under config/initializers (you can name it whatever you want) and specify the YML file which holds the values for you class (config/app_config.yml is used in this example):

{% highlight ruby %}
AppConfig = YAML.load_file(File.join(RAILS_ROOT, 'config', 'app_config.yml'))
{% endhighlight %}

This will open up the class AppConfig with several attributes for you to use throughout your app, for example:

{% highlight ruby %}
app_name: My application name
base_domain: myapp.com
twitter_app_token: Your app token
{% endhighlight %}

Each of these values will become an attribute for your AppConfig class:

{% highlight bash %}
AppConfig.app_name
#=> "My application name"

AppConfig.base_domain
#=> "myapp.com"
{% endhighlight %}

You can take advantage of YAML's syntax and use indentation to create a hash of values:

{% highlight ruby %}
defaults: &defaults
app_name: My application name
base_domain: myapp.com

development:
  <<: *defaults
  twitter_app_dev_key: Your app key
  twitter_app_dev_secret: Your app secret

production:
  <<: *defaults
  twitter_app_prod_key: Your app key
  twitter_app_prod_secret: Your app secret
{% endhighlight %}

Now your class has some hashes values like this:

{% highlight bash %}
AppConfig['development']['twitter_app_prod_key']
#=> "Your app key"

AppConfig[Rail.env]['twitter_prod_secret']
#=> "Your app secret"
{% endhighlight %}

## Enjoy!
