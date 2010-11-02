---
layout: post
title: Controller responders in Rails 3
author: Luis Galaviz
email: luis.galaviz@crowdint.com
avatar: 1e7f8fb8733b6193cf3bdbc85693f515
---

When Rails 3 was released I was expecting several changes, most of them had me excited. I was aware Rails 3 would be even more reliable and tied to it's main concept "Convention over configuration". What could I say? I'm believer!

Well, I'm impressed the most with the new even DRYer way Rails works with Controllers. Let me explain why.

Imagine you are creating a basic controller:

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

  def edit
    @variant = Variant.find(params[:id])
  end

  def update
    @variant = Variant.find(params[:id])
    if @variant.update_attributes(params[:variant])
      flash[:notice] = "Succesfully updated."
      redirect_to variant_path
    else
      render :action => "edit"
    end
  end
end
{% endhighlight %}

Or, maybe, you could use Rails 3 dryness (sounds cool, isn't it?):

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
    @variant = Variant.new(params[:variant])
    flash[:notice] = "Fortunately it was saved!" if @variant.save
    respond_with(@variant)
  end

  def edit
    respond_with(@variant = Variant.find(params[:id]))
  end

  def update
    @variant = Variant.find(params[:id])
    flash[:notice] = "Succesfully updated." if @variant.update_attributes(params[:variant])
    respond_with(@variant)
  end
  
end
{% endhighlight %}

Looks DRYer, but not enough already. Let's try to do this again, but first we need to do something:

{% highlight bash %}
$ gem install responders

or, in your Gemfile

gem 'responders'
{% endhighlight %}

Now, configure your responders:

{% highlight bash %}
rails generate responders:install
{% endhighlight %}

Are you ready? Ok, let's give it a try:

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
    respond_with(@variant = Variant.create(params[:variant]), :location => variants_path)
  end

  def edit
    respond_with(@variant = Variant.find(params[:id]))
  end

  def update
    @variant = Variant.find(params[:id])
    respond_with(@variant.update_attributes(params[:variant])
  end

end
{% endhighlight %}

Wow! That was DRY!

# Well, we already saw Rails 3 controllers could help you. But, how does it work?

Let's analyze step by step.

## 1. Old respond_to

We used to use respond_to to work with different mime requests:

{% highlight bash %}
def index
  @variants = Variant.all
  respond_to do |format|
    format.html
    format.xml { render :xml => @variants }
    format.json { render :json => @variants }
  end
end
{% endhighlight %}

Now, we just use respond_with as follows, and it will give the same result:

{% highlight bash %}
respond_to :html, :xml, :json

def index
  respond_with(@variants = Variant.all)
end
{% endhighlight %}


## 2. When do I need to use respond_with?

You will use it when:

* You want you controllers to be as DRY as possible. Using the principle convention over configuration, you could simplify your controllers, an make them easy to maintain and to read for others (and you as well).
* When you have to respond to many different mime requests. You can even customize special actions for each response method, example:

{% highlight bash %}
respond_to :html, :xml


def create
  @variant = Variant.new(params[:variant])
  flash[:notice] = "Fortunately it was saved!" if @variant.save
  respond_with(@variant) do |format|
    format.html { redirect_to variants_url }
    format.xml { render @variant }
  end
end

or, we could use :location to redirect to the same location:

def create
  @variant = Variant.create(params[:variant])
  flash[:notice] = "Fortunately it was saved!" if @variant.save
  respond_with(@variant, :location => variants_path)
end

{% endhighlight %}

If you get complicated when customizing a respond_with I recommend you, stop customizing and go back to the old configuration.

## Ok, now we know why respond_with. But, why do we need responders gem?

Once you are using respond_with you might still keep using manual flash (notice, alert) messages. But, with responders gem you will use automatic response messages configured trough the i18n. When you install this gem you will customize your flash messages using your local .yml. It is configured as follows:

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

If you want more information about how this gem works you could visit his [source](http://github.com/plataformatec/responders). Also, if you want to know more about respond_with you could visit [Ryan Daigle's](http://ryandaigle.com/) posts, they are really helpful. 














