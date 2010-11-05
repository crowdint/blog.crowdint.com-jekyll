---
layout: post
title: Controller responders in Rails 3
author: Luis Galaviz
email: luis.galaviz@crowdint.com
avatar: 1e7f8fb8733b6193cf3bdbc85693f515
---

When Rails 3 was released I was expecting several changes, most of them had me excited. I was aware Rails 3 would be even more reliable and tied to it's main concept "Convention over configuration". What could I say? I'm believer!

Well, I'm impressed the most with the new even DRYer way Rails works with Controllers. Let me explain why.

Imagine you are creating a basic RESTFUL controller, you will start with:

{% highlight bash %}
class VariantsController < ApplicationController

  def index
    @variants = Variant.all
  end
  
  def new
    @variant = Variant.new
  end
  
  def create
    @variant = Variant.new(params[:variant])
    if @variant.save
      flash[:notice] = "Fortunately it was saved!"
      redirect_to variant_path
    else
      render :action => "new"
    end
  end
  .
  .
  .
{% endhighlight %}

Once you finish, you will have the seven necessary actions (index, new, create, show, edit, update and destroy). Now imagine you have to respond to different mime requests, like xml, json, html. So, in your controller you may have something like this:

{% highlight bash %}
class VariantsController < ApplicationController

  def index
    @users = User.all
    respond_to do |format|
      format.html
      format.xml { render :xml => @users }
    end
  end
  .
  .
  .

  def create
    @variant = Variant.new(params[:variant])
    respond_to do |format|
      if @user.save
        flash[:notice] = 'Fortunately it was saved!'
        format.html { redirect_to variant_path) }
        format.xml { render :xml => @varian, :status => :created, :location => @variant }
      else
        format.html { render :action => "new" }
        format.xml { render :xml => @variant.errors, :status => :unprocessable_entity }
      end
    end
  end
  .
  .
  .
{% endhighlight %}

Now our controller looks overweighted. So, how could we solve this predicament? We need to respond to different mime requests, but at the same time we want our controllers thin. Ok, it's time to respond_with to come to the rescue.

First, we have to define which mime responses our controller has to respond. In this case are html and xml, so we have to define:

{% highlight bash %}
class VariantsController < ApplicationController
  respond_to :html, :xml
  .
  .
  .
  
{% endhighlight %}

Then, we will use respond_with to set the response, like follows:

{% highlight bash %}
def index
  respond_with(@variants = Variant.all)
end

def new
  respond_with(@variant = Variant.new)
end

def create
  @variant = Variant.create(params[:variant])
  flash[:notice] = 'Fortunately it was saved!' if @user.save
  respond_with(@variant)
end
.
.
.
{% endhighlight %}

## When do I need to use respond_with?

You need to use it when:

* You want your controllers to be as DRY as possible. Avoiding rewrite as much code as possible. Using the principle convention over configuration, you could simplify your controllers, and make them easy to maintain and to read for others (and you as well).
* When you have to respond to many different mime requests. You can even customize your response_with. For example, you could add the same destination path to your responses adding :location as param:

{% highlight bash %}
def create
  respond_with(@variant = Variant.create(params[:variant]), :location => variants_path)
end
{% endhighlight %}

or, you could specify different actions for each response:

{% highlight bash %}
def create
  respond_with(@variant = Variant.create(params[:variant])) do |format|
    format.html
    format.xml { render :xml => @users }
  end
end
{% endhighlight %}

If you get complicated when customizing a respond_with I recommend you, stop customizing and go back to the old configuration.

Other tool that plays very well with this new controllers way is the gem [responders](http://github.com/plataformatec/responders). Once you are using respond_with you might still keep using manual flash (notice, alert) messages. But, with responders gem you will use automatic response messages configured trough the i18n. When you install this gem you will customize your flash messages using your local yml. Just install the gem:

{% highlight bash %}
$ gem install responders

or, in your Gemfile

gem 'responders'
{% endhighlight %}

and then, configure it through the appropriate generator:

{% highlight bash %}
rails generate responders:install
{% endhighlight %}

and you will see this changes:

{% highlight bash %}
create  lib/application_responder.rb
prepend  app/controllers/application_controller.rb
inject  app/controllers/application_controller.rb
create  config/locales/responders.en.yml
{% endhighlight %}

what we will use is the last file "responders.en.yml", this file contains the messages for each action of the responders, and it looks like this:

{% highlight bash %}
en:
  flash:
    actions:
      create:
        notice: '%{resource_name} was successfully created.'
      update:
        notice: '%{resource_name} was successfully updated.'
      destroy:
        notice: '%{resource_name} was successfully destroyed.'
        alert: '%{resource_name} could not be destroyed.'
{% endhighlight %}

You can manipulate this yml, following the i18n conventions, configuring notice messages or alert, or configure personalized messages for each resource, etc.

Ok, so, back to the controller, yo could define the create action as follows:

{% highlight bash %}
def create
  respond_with(@variant = Variant.create(params[:variant]))
end
{% endhighlight %}

And this way you could have, even, the flash notice.

So, at the end, this  could be your complete RESTFUL controller:

{% highlight bash %}
class VariantsController < ApplicationController
  respond_to :html

  def index
    respond_with(@variants = Variant.all)
  end

  def new
    respond_with(@variant = Variant.new)
  end

  def create
    respond_with(@variant = Variant.create(params[:variant]))
  end

  def edit
    respond_with(@variant = Variant.find(params[:id]))
  end

  def update
    @variant = Variant.find(params[:id])
    respond_with(@variant.update_attributes(params[:variant]))
  end
  
  def destroy
    @variant = Variant.find(params[:id])
    @variant.destroy
    respond_with(@variant)
  end
  
end
{% endhighlight %}

## What else?

Well, if you want to define a particular response by default, you could set in your "routes.rb" this:

{% highlight bash %}
resources :variants, :defaults => {:action => "index", :format => "xml"}
{% endhighlight %}

If you define a view for this format (example index.xml.haml) it will render it. But if not, then it will invoke to_xml to render it.

Try to play with the new controllers behavior, it might surprise you. If you want to know more about respond_with you could visit [Ryan Daigle's](http://ryandaigle.com/) posts, they are really helpful.

