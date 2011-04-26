---
layout: post
title: How to override destroy method and still using callbacks 
author: Ana Rosas & Omar Vargas
email: ana.rosas+omar.vargas@crowdint.com
avatar: ec9ab4537f5224def5fc54cd7465008b
---
While working on a project, in which we were required to override the
destroy method in order to create a soft delete method, in other words
to change the active state of the object to deleted.

That was no problem, the issues came when we needed this method to use
callbacks for the counter cache to work.

In Rails 2 you can override destroy method without affecting callbacks,
this by overriding the method *destroy_witout_callbacks*

{% highlight ruby %}
  def destroy_without_callbacks
    unless new_record?
      # your code here
    end
    freeze
  end
{% endhighlight %}

But in Rails 3 things have changed and the previuos methods doesn't
work. So after some research, we found out that you can call method's
callbacks with run_callbacks method, from ActiveSupport method

{% highlight ruby %}
    def destroy
      run_callbacks :destroy do
        # your code here
      end
    end
{% endhighlight %}

In this case we use it for the destroy method, but you can use it for
any method you override and need their callbacks. If you don't specify
any callbacks, all callbacks will be called.

You can find more info
[here](http://api.rubyonrails.org/classes/ActiveSupport/Callbacks.html)

Hope this may help you in your overriding tasks
