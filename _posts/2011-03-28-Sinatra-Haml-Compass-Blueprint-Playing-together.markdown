---
layout: post
title: How to Sinatra + Haml + Compass(SCSS) + Blueprints
author: Hector Bustillos
email: hector.bustillos@crowdint.com
avatar: fa56812143dd6049ef19d1a8032f21da
publish: false
---

During the past week, I spent about half a day trying to get Sinatra working 
with Compass and Blueprint. At first it was a pain in the ass, because the 
documentations are pretty lame. But after sometime reading here and there, I 
proceeded to adding command lines and saw how it didn't work, unfortunately. 
But, there is hope... I got it working and here is how I did it:

First of all you have to setup your sinatra app as usual, if you have read
[Omar's post about setting up your sinatra app with test](http://blog.crowdint.com/2011/03/14/Sinatra-the-green-way.html) you know what Im 
talking about, if haven't yet, take a look at it.

It is very easy to setup your sinatra apps, a couple of minutes and it's done

You can check a ready-to-fork project to use [Sinatra+Haml+Compas+Blueprint](http://github.com/hecbuma/sinatra-blueprint) and make your changes there.

## The real deal

Ok, now that we have our sinatra app, it's time to add some stuff to be able 
to use Haml and Compass.

First add some gems to you gemfile (Compass and Haml). You will be end with something like this:
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

Now you have to add config file for compass:

    $ mkdir config 
    $ touch compass.rb

and add this content to the compass.rb:

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
Note that I've changed the css_dir and the image dir to use "Public" instead of "Static


Ok now we are half way there.

Now let's add some more config for compass but now in your app file (myapp.rb):

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
With this you are converting the SCSS into CSS files.


And the final touch: let's setup blueprint for this project.

Lets going to setup blueprint for this project. 
    $ compass install blueprint
we are adding the required files for blueprint

And that's all. I hope this helps you.










