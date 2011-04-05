---
layout: post
title: How to Sinatra + Haml + Compass(SCSS) + Blueprints
author: Hector Bustillos
email: hector.bustillos@crowdint.com
avatar: fa56812143dd6049ef19d1a8032f21da
publish: false
---

Last week, I spent about half a day trying to get Sinatra working
with Compass and Blueprint. At first, it was a pain in the ass because the
documentation is pretty lame. But after some reading here and there, I
tried by adding it via command line and I failed again.

But, there is hope... I got it working and here is how I did it:

First of all, you have to setup your sinatra app as usual.
If you have read
[Omar's post about setting up your sinatra app with test](http://blog.crowdint.com/2011/03/14/Sinatra-the-green-way.html)
you know what I'm talking about. Take a look at it if you haven't.

It is very easy to setup your sinatra apps, a couple of minutes and you're done.

You can also fork my ready-to-use github repo [Sinatra+Haml+Compas+Blueprint](http://github.com/hecbuma/sinatra-blueprint)
and make your changes there.

## The real deal

Ok, now that we have our sinatra app, it's time to add some stuff so we are able
to use Haml and Compass.

First add these gems to you Gemfile (Compass and Haml). You will end up with something like this:
{% highlight ruby %}
  source "http://rubygems.org"

  gem 'compass' 
  gem 'sinatra'
  gem 'haml'
  gem 'cucumber'
  gem 'cucumber-sinatra'
  gem 'capybara'
  gem 'rspec'
{% endhighlight %} 

Now you have to add a config file for compass:

    $ mkdir config
    $ touch compass.rb

and add this content to it:

{% highlight ruby %}
if defined?(Sinatra)
  # This is the configuration to use when running within sinatra
  project_path = Sinatra::Application.root
  environment = :development
else
  # this is the configuration to use when running within the compass command line tool.
  css_dir = File.join 'public', 'stylesheets'
  relative_assets = true
  environment = :production
end

# This is common configuration
sass_dir = File.join 'views', 'stylesheets'
images_dir = File.join 'public', 'images'
http_path = "/"
http_images_path = "/images"
http_stylesheets_path = "/stylesheets"
{% endhighlight %}

Note that I've changed the css_dir and the image dir to use "public" instead of "static"

Ok, now we are half way there.

Now, let's add some more configuration for compass, but this time in your app file (myapp.rb):

{% highlight ruby %}
require 'compass'
require 'sinatra'
require 'haml'

configure do
  set :haml, {:format => :html5, :escape_html => true}
  set :scss, {:style => :compact, :debug_info => false}
  Compass.add_project_configuration(File.join(Sinatra::Application.root, 'config', 'compass.rb'))
end

get '/stylesheets/:name.css' do
  content_type 'text/css', :charset => 'utf-8'
  scss(:"stylesheets/#{params[:name]}" ) 
end

get '/' do
  haml :index
end
{% endhighlight %}

With this you are telling compass to automatically convert the SCSS files into CSS files.

And the final touch: let's setup blueprint for this project.

{% highlight bash %}
    $ compass install blueprint
{% endhighlight %}

And that is all. I hope this is helpful for you.










