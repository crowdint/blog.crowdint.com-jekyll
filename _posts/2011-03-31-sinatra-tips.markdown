---
layout: post
title: Trying to make your Sinatra more RESTful?
author: Mario Zaizar
email: mario@crowdint.com
avatar: cd962d009c918bfc2dc6e63fc1b3b5cb
---

> <small>`TODO. Write introductions. Include references to some RESTul tutorial. Explain why RESTul is so important. Add a disclaimer, pray for feedback.
> Make funny jokes, like "Why this guy wears a Mexican hat?"`</small>

No RESTul knowledge at all? [read this!][rest_rails]

### REST URLs should be multi-response
In rails we have [`respond_to(format)`][respond_to] to switch between different layouts, and partials. 
So, just create a helper like `request_type?` and switch between views!

{% highlight ruby %}
# Route
get '/book/:title/chapters'
  case request_type?
    when :mobile  then haml :'mobile/chapters', :layout => :'mobile/layout'
    when :ajax    then haml :chapters, :layout => false # json
    else haml :chapters, :layout => :'static/layout'
  end
end

# Helper
def request_type?
  return :mobile  if mobile_request?
  return :ajax    if request.xhr?
  return :normal
end
{% endhighlight %}

Avoid having different routes for the same `resource/action`, like `/m/register` and `/user/new` doing the same thing. 
Create one route than actually makes sense, and just play with the response type. 

> Or even simpler!
> Thanks to [Emmanuel](http://twitter.com/chischaschos)

{% highlight ruby %}
view, layout = mobile_request? ? 
    [:'mobile/chapters', :'mobile/layout'] : 
    [:'chapters', false]
haml view, layout => layout
{% endhighlight %}

### "Fat models, skinny <strike>controllers</strike> routes"
Move your ugly controller-based validations [to your models][fat_models], like:
{% highlight ruby %}
# This is wrong
post 'user/new' do
  halt('user exists') if user.count(:email => params[email])
  halt('empty email') if params[email].blank?
  @user = User.new(params)
end
{% endhighlight %}

Use helpers like [`error_messages_for(@user)`]() with direct model's validates_for: 
{% highlight ruby %}
# Model
class User
  validate_uniqueness_for :email
  validate_presence_for :email
  ...
end

# Route
post 'user/name' do
  @user = user.new params
  unless @user.save
    errors = error_messages_for(@user)
    # => 'Email can't be blank and it's already taken.'
  end
end

# Helpers
def error_messages_for(object)
  all_errors = ""
  for error in object.errors.errors do
    key = error.first.to_s.capitalize
    what_is_wrong = error.second.join(' and ')
    all_errors += "#{key} #{what_is_wrong}.\n"
  end
  all_errors
end
{% endhighlight %}

### Start using route "Filters"
If you are using the same sentence in a several routes, like:

{% highlight ruby %}
# /user/001/ + dash
get '/user/:uid/dash' do
  require_login! if current_user.logged?
  ...
end
# /user/001/ + other
get '/user/:uid/other' do
  require_login! if current_user.logged?
  ...
end
{% endhighlight %}

Start using [Sinatra's filters][sinatra-filters] as `before` or `after`. They also can be string matched! like `before '/this/*'` (wildcards) or `after /^(this|other)$/` (regex)

{% highlight ruby %}
# /user/001/*
before '/user/:uid/*' do
  require_login! if current_user.logged?
end

get '/user/:uid/dash' do ... end
get '/user/:uid/other' do ... end
{% endhighlight %}

### Better error messaging
Improve your multi-response routes, handling the correct messages for.
<u>For example</u> use flash/notices for mobiles, json for ajax or jGrowls, and so on...

{% highlight ruby %}
post '/users/new' do
  ...
  case request_type?
    when :mobile
      flash[:error] = 'Ups, something goes wrong.'
      redirect back
    when :ajax
      { :message => 'Ups, something goes wrong.', :status => 401 }.to_json
    end
  end
end
{% endhighlight %}

### Doing the same thing, but using different requests?
Are you doing the same code, with the same route but only changing the http-verb? 
I do, and I found this: [Multi-Routing in Sinatra](http://nuclearsquid.com/writings/multi-routing.html) 
to use 'any' when you need the same route action for different methods (http verbs). 

For example having this routes:
{% highlight ruby %}
get '/user/:id' do ... end
post '/user/:id' do ... end
put '/user/:id' do ... end
delete '/user/:id' do ... end
{% endhighlight %}

You can use:
{% highlight ruby %}
any '/user/:id' do ... end
{% endhighlight %}

### Be flexible with your route endings
> I recommend enable your routes with/without last slash "/", 
> but I found that some RESTul projects prefer all urls without slashes. 
> So, there is a separate opinions... [1][si_slash], [2][no_slash]
> The true is, adding an extra slash to the end isn't a big deal. Don't think so? 

For example having:
{% highlight ruby %}
# This will rais 404 using '/users/001/friends/'
get '/users/:uid/friends' do
  ...
end 
{% endhighlight %}

The solution it’s pretty simple, just add `/?` to the end of your routes.
{% highlight ruby %}
# this will work with: /users/:uid/friends and /users/:uid/friends/
get '/users/:uid/friends/?' do
  ...
end
{% endhighlight %}

## See you next time!
May the Source Be With You.

## References
- [RESTful Rails Development](rest_rails) Excellent tutorial about RESTul and Rails

[rest_rails]: http://www.b-simple.de/documents
[si_slash]: http://www.standardzilla.com/2007/07/09/dont-forget-your-trailing-slash/]
[no_slash]: http://www.projectzero.org/sMash/1.0.x/docs/zero.devguide.doc/zero.core/REST.html]
[respond_to]: http://www.tokumine.com/2009/09/13/how-does-respond_to-work-in-the-rails-controllers/
[fat_models]: http://weblog.jamisbuck.org/2006/10/18/skinny-controller-fat-model
[sinatra-filters]: http://sinatra-book.gittr.com/
