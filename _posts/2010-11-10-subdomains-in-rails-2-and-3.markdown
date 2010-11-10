---
layout: post
title: Subdomains in Rails (2.3.x & 3)
author: Francisco Guzm&aacute;n
email: francisco.guzman@crowdint.com
avatar: c000ffd0c4ed3e23d09cae624d24b525
published: false
---

#Why?

Knowing how to handle subdomains in your Rails app is a very valuable asset in your toolbox; chances are that any given time you'll need to offer them in your well polished-deployed to production service. So instead of having [Facebook-like urls =>  http://www.facebook.com/j.francisco.guzman](http://www.facebook.com/j.francisco.guzman), you may want to have something like [http://francisco.expensd.com](http://francisco.expensd.com).

This can be particularly useful and shiny in two cases:

● You want to have subdomains such as: admin, blog, mail, etc. <br />
● You want to provide your users with a subdomain they can come up with <br />

Want to give it a shot?

##Sounds cool, but I still use Rails 2.3.x

Well, your best friend then is [SubdomainFu](http://github.com/mbleigh/subdomain-fu); according to its [GitHub](http://github.com/mbleigh/subdomain-fu) resource, it "_provides a modern implementation of subdomain handling in Rails. It takes aspects from account\_location, request\_routing, and other snippets found around the web and combines them to provide a single, simple solution for subdomain-based route and url management._"

You can either install it as an 'old school' plugin or a more fashion-like gem:

###Plugin
{% highlight bash %}
script/plugin install git://github.com/mbleigh/subdomain-fu.git
{% endhighlight %}

###Gem (no Bundle) -- add it to your config/environment.rb file 
{% highlight ruby %}
config.gem 'subdomain-fu'
{% endhighlight %}
and then...
{% highlight bash %}
rake gems:install
{% endhighlight %}

###Gem (Bundle) -- add it to your Gemfile
{% highlight ruby %}
gem 'subdomain-fu'
{% endhighlight %}
and then...
{% highlight bash %}
bundle install
{% endhighlight %}


##Settings (we're still working with subdomains in Rails 2.3.x)
What SubdomainFu provides is an extension for the URL Rewriting engine provided with Rails. This means we will be able to use it anywhere we normally generate URLs: _url\_for_, named routes, etc. So here's the basic configuration (with default values):

{% highlight ruby %}
# in environment.rb
  
# These are the sizes of the domain (i.e. 0 for localhost, 1 for something.com)
# for each of your environments
SubdomainFu.tld_sizes = { :development => 0,
                          :test => 0,
                          :production => 1 }

# These are the subdomains that will be equivalent to no subdomain
SubdomainFu.mirrors = ["www"]

# This is the "preferred mirror" if you would rather show this subdomain
# in the URL than no subdomain at all.
SubdomainFu.preferred_mirror = "www"
{% endhighlight %}

###What about local dev environment???
There are two different ways to set up your local environment to work with subdomain:

● Editing your hosts file (usually located in /etc/hosts) and add each subdomain you will need (I know it doesn't sound like a great idea)

● Using [Tim Pope's](http://tbaggery.com/2010/03/04/smack-a-ho-st.html) awsome contributon to work with subdomains in our local environments; doing so, will allow us to use [http://lvh.me:3000]() with subdomains

[http://cool.lvh.me:3000]()

##Been there, done that... now what???
Now you can take advantage of two awesome features: the _current\_subdomain_ method which will return (of course) the current subdomain or _nil_ if there is no subdomain or the current subdomain is a mirror:

{% highlight bash %}
# http://admin.killerapp.com/
current_subdomain # => "admin"

# http://www.killerapp.com/ or http://killerapp.com/
current_subdomain # => nil

# http://other.subdomain.killerapp.com
current_subdomain # => "other.subdomain"
{% endhighlight %}

and the-now-extended URL rewriting features passing the **:subdomain** option to an URL generating method:

{% highlight ruby %}
url_for(:controller => "da_controller", 
  :action => "cool_feature", 
  :subdomain => "admin") # => http://admin.mykillerapp.com/da_controller/cool_feature
  
users_url(:subdomain => false)  # => http://mykillerapp.com/users

# The full URL will be generated if the subdomain is not the same as the
# current subdomain, regardless of whether _path or _url is used.
users_path(:subdomain => "admin") # => http://admin.mykillerapp.com/users
users_path(:subdomain => false) # => /users
{% endhighlight %}

##Generating subdomains
You have two options (again): reserving yourself the right to create them manually or else, giving your users the ability to choose their own subdomain, checking availability and storing the value to a document or db table... you now have the tool, how to use it is up to you.

Enjoy!

#Enjoy??? Wait!!! What about Rails 3???

##For our edgy Rails 3 users
It is a real blessing the guys who worked on Rails 3 decided to incorporate this feature so we can use it out-of-the-box taking advantage of the new routing DSL. A great example we can use is a multiple blog app, where we can have blogs (subdomains), articles and comments:

**in config/routes.rb**
{% highlight ruby %}
MyKillerApp::Application.routes.draw do |map|  
  resources :comments  
  resources :articles  
  resources :blogs  
  root :to => "blogs#index"  
end
{% endhighlight %}

In this example, our root_path will land in **blogs#index**. We would want this only if no subdomain has been specified, otherwise we want to perform the action **blogs#show**. Rails 3 makes it really easy by adding a restriction:

{% highlight ruby %}
MyKillerApp::Application.routes.draw do |map|  
  resources :comments  
  resources :articles  
  resources :blogs  
  match '/' => 'blogs#show', :constraints => { :subdomain => /.+/ }  
  root :to => "blogs#index"  
end
{% endhighlight %}

This setting demands our attention in two details: the _id_ that will be passed to the _show\_path_ and the _subdomain_ **:constraint**

To pass the id we just need to use a finder like this:

{% highlight ruby %}
def show
  @blog = Blog.find_by_subdomain(request.subdomain)
end
{% endhighlight %}

**_voilà_**

As for the subdomain, we will only pass it in our request when we need it; that simple!

##Mirrors (the 'www' case)

In Rails 2.3.x, SubdomainFu handles this specific case in a really cool-simple way; in Rails 3 this is accomplished in a little bit of a complex way, but not way too complex...

First, we turn the subdomain constraints into a _block_ in our **config/routes.rb** file

{% highlight ruby %}
MyKillerApp::Application.routes.draw do |map|  
  resources :comments  
  resources :articles  
  resources :blogs
  constraints(Subdomain) do  
    match '/' => 'blogs#show'
  end
  root :to => "blogs#index"  
end
{% endhighlight %}

and we pass a new class called **Subdomain**, which we'll define in our **lib** folder (**lib/subdomain.rb**)

{% highlight ruby %}
class Subdomain  
  def self.matches?(request)  
    request.subdomain.present? && request.subdomain != 'www'  
  end  
end
{% endhighlight %}

**_done!_**

###Links
To use subdomains in our links, we can not just pass a **:subdomain** option like we can with _SubdomainFu_; instead, we must build the host name from scratch and adding the subdomain. This is accomplished using the _subdomain_ attribute in our **blog** object + the request domain + the request port:

**_This won't work..._**
{% highlight erb %}
<% title "Blogs" %>  
  
<% for blog in @blogs %>  
  <div>  
    <h2><%= link_to blog.name, blog %></h2>  
    <div class="actions">  
      <%= link_to "Edit", edit_blog_path(blog) %> |   
      <%= link_to "Destroy", blog, :confirm => 'Are you sure?', :method => :delete %>  
    </div>  
  </div>
<% end %>
{% endhighlight %}

**_but this will..._**
{% highlight erb %}
<% title "Blogs" %>  
  
<% for blog in @blogs %>  
  <div>  
    <h2><%= link_to blog.name, root_url(:host => "#{blog.subdomain}.#{request.domain}.#{request.port_string}") %></h2>  
    <div class="actions">  
      <%= link_to "Edit", edit_blog_path(blog) %> |   
      <%= link_to "Destroy", blog, :confirm => 'Are you sure?', :method => :delete %>  
    </div>  
  </div>
<% end %>
{% endhighlight %}

**_... we just need to make it look cleaner... with a helper!_**
{% highlight erb %}
<h2><%= link_to blog.name, root_url(:host => with_subdomain(blog.subdomain)) %></h2>
{% endhighlight %}

**_the helper must be located in_** **app/helpers/url_helper.rb**
{% highlight ruby %}
module UrlHelper  
  def with_subdomain(subdomain)  
    subdomain = (subdomain || "")  
    subdomain += "." unless subdomain.empty?  
    [subdomain, request.domain, request.port_string].join  
  end  
end
{% endhighlight %}

**_and include this module in_** **ApplicationController** **_to make it available to all controllers in our app..._**

{% highlight ruby %}
class ApplicationController < ActionController::Base
  include UrlHelper
  #other includes, protect_from_forgery, layout, etc.
end
{% endhighlight %}

##That should be enough to get you started

Enjoy!

##Resources
**Rails 2.3.x** <br />
[SubdomainFu](http://github.com/mbleigh/subdomain-fu) <br />
[Railscasts](http://railscasts.com/episodes/123-subdomains) <br />
[Intridea](http://intridea.com/2008/6/23/subdomainfu-a-new-way-to-tame-the-subdomain) _the guys who created SubdomainFu_

**Rails 3** <br />
[Railscasts](http://railscasts.com/episodes/221-subdomains-in-rails-3) <br />
[Asciicasts](http://asciicasts.com/episodes/221-subdomains-in-rails-3) <br />

**Local Virtual Host** <br />
[Post](http://tbaggery.com/2010/03/04/smack-a-ho-st.html)

